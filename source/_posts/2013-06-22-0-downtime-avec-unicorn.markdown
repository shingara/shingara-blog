---
layout: post
title: "0 Downtime avec Unicorn"
date: 2013-06-22 15:50
comments: true
categories:
  - unicorn
  - deploiement
  - ruby
---
J'ai eu la chance de participer au premier (rendez vous BBL Paris)[http://ruby-paris-bbl.tumblr.com/].
Dans le cadre de cette rencontre, nous avons parler de nos processus de déploiement.
Je vais essayer de faire quelques billets sur mes processus de déploiement.

# Le Server web

Pour déployer une application ruby, il nous faut un serveur web. Il en
existe beaucoup dans l'écosystème ruby. Passenger, Thin,
(Unicorn)[http://unicorn.bogomips.org/], Puma
sont les plus connus. J'utilise personnelement unicorn depuis plusieurs
années. Mon choix a surtout été fait suite à la possbilité d'avoir un 0
Downtime. J'utilisais précédement thin et la mise en place d'un 0
downtime est assez compliqué. Pour Unicorn, tout est fait en interne.
C'est une fonctionnalité de base.

# Comment réaliser ce 0 downtime ?

Pour expliquer comment ceci est possible, il faut comprendre
(l'architecture de Unicorn)[http://unicorn.bogomips.org/DESIGN.html].
Unicorn a un système maître / workers. Au démarrage de unicorn, un processus maitre se lance et
lance une série de processus par fork(2) suivant la configuration. Tous
ces processus seront ceux qui exécuteront réelement votre application.
Le processus maitre ne sert qu'à gérer les dispatching entre les
différents workers.

L'avantage de cette architecture est que vous n'avez pas besoin de
load-balancing devant votre unicorn. C'est unicorn lui-même qui gére le
système.

Mais ce qui est très pratique est que le processus maitre comprend très
bien les (signaux UNIX)[http://unicorn.bogomips.org/SIGNALS.html]. Les signaux UNIX sont hélas très souvent
sous-utilisé alors qu'ils sont très puissant.

Le signal le plus utile pour notre 0 downtime est le USR2. D'après la
documentation, "relance l'executable binaire. Un QUIT doit être envoyé
au process original une fois que l'enfant a été constaté comme UP et lancé".
En résumé, quand le processus maitre recoit le SIGNAUX USR2, il relance un
nouveau process maitre. Par contre, la documentation explique bien qu'il
faut penser a lancer un SIGNAUX QUIT au process initial pour qu'il
s'arrête réelement.

Pour réaliser ce dernier point, il faut donc penser à configurer
correctement unicorn pour qu'il fasse ce QUIT. Voici donc la
configuration que j'utilise pour ça.

```
before_fork do |server, worker|

  old_pid = "pids/unicorn.pid.oldbin"
  if File.exists?(old_pid) && server.pid != old_pid
    begin
      Process.kill("QUIT", File.read(old_pid).to_i)
    rescue Errno::ENOENT, Errno::ESRCH
      # someone else did our job for us
    end
  end

end
```

le `before_fork` se lance une fois que le processus maitre est UP et
qu'il va commencer a créer un fork pour avoir un travailleur.

Quant à lui la réception d'un SIGNAL QUIT arrête les processus Unicorn
une fois qu'il a fini de traiter toutes les requêtes en cours. C'est ce
méchanisme qui nous permet le 0 downtime.

# Le problème du Gemfile

Toutes les personnes qui n'utilise pas ce méchanisme, ne le font pas
suite à un problème de configuration avec Bundler. Une (documentation
compléte existe à ce sujet)[http://unicorn.bogomips.org/Sandbox.html].

En gros, la technique est simplement de penser à définir le path the
Gemfile directement par la variable d'environement `BUNDLE_GEMFILE`.
Soit lors du démarrage du process.

```
BUNDLE_GEMFILE=./Gemfile unicorn -c config/unicorn.rb
```

Soit directement dans le fichier de configuration de unicorn

```
before_exec do |server|
  ENV["BUNDLE_GEMFILE"] = "./Gemfile"
end
```

Une fois que vous avez configurer correctement votre Gemfile, vous
n'avez plus de problème. Le seul cas que j'ai parfois pu rencontrer
avant de définir correctement mon `BUNDLE_GEMFILE` était simplement que
le nouveau process unicorn ne se lancait pas ne trouvant pas la bonne
gem. En conséquence, il suffit cette fois ci de faire un restart de mon
processus. Il me suffit juste de vérifier que mon process unicorn est
bien à jour. était simplement que le nouveau process unicorn ne se
lancait pas ne trouvant pas la bonne gem. En conséquence, il suffit
cette fois ci de faire un restart de mon processus. Il me suffit juste
de vérifier que mon process unicorn est bien à jour par un simple :

```
ps o "start command" | grep unicorn
```

# Autre problème possible

Pour les malchanceux comme moi qui ont des applications qui ne sont pas
encore en rails > 3.0, attention il faut penser à utiliser l'executable
`unicorn_rails`.

J'espére vous avoir faire découvrir mes petits astuces sur Unicorn et
que cela vous sera utile.
