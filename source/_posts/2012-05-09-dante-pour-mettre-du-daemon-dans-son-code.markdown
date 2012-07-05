---
layout: post
title: "dante pour mettre du daemon dans son code"
date: 2012-05-09 09:43
comments: true
categories:
  - dante
  - gem
  - ruby
  - daemon
---
J'ai dernièrement eu besoin de convertir un petit script ruby en daemon.

J'utilise dans mes déploiements monitrc pour le monitoring de mes
scripts. Monit check un pidfile pour voir si le processus est actif ou
pas.

J'ai donc cherché en Ruby quel était la meilleure solution pour que mon
script soit un daemon et puisse générer lui-même son pidfile.

Le projet le plus connu est [daemons](http://daemons.rubyforge.org/).
J'ai déjà essayé de l'utiliser, mais je n'en ai jamais été un grand
convaincu. Je l'ai toujours trouvé compliqué. Il ne gére pas la
génération de pidfile. Il faut donc pratiquement tout gérer soit même.

Le deuxième projet le plus connu est une sur-couche de daemons c'est
[daemon-kit](https://github.com/kennethkalmer/daemon-kit). J'avais déjà
tenté de l'utiliser, mais je le trouve vraiment trop rigide pour un
simple script. C'est selon moi plus une massue qu'autre chose pour créer
un daemon.

C'est alors que je me suis rappelé avoir entendu parlé d'un nouveau gem
pour créer des daemons. J'ai cherché et j'ai ainsi pu découvrir
[dante](https://github.com/bazaarlabs/dante). Ce projet est exactement
ce que je cherchais. Il est simple et fait le boulot de créer un daemon.


Par défaut, il supporte quelques arguments à passer en ligne de
commande. Ces arguments font l'essentiel du travail d'un daemon.

Voici une liste des arguments par défaut disponible.

```
    -p, --port PORT           Specify port
                              (default: )
    -P, --pid FILE            save PID in FILE when using -d option.
                              (default: /var/run/scheduler.pid)
    -d, --daemon              Daemonize mode
    -l, --log FILE            Logfile for output
    -k, --kill [PORT]         Kill specified running daemons - leave
blank to kill all.
    -u, --user USER           User to run as
    -G, --group GROUP         Group to run as
    -?, --help                Display this usage information.
```

On peux ainsi définir, le PID, le fichier de log, l'utilisateur et le
group lançant le daemon ( si lancé en root ). Seul petit argument qui ne
me semble pas essentiel, c'est l'argument du port. Mais l'usage initial
de dante est de lancé des applications rack. Voila pourquoi on
retrouve cet argument.

Une de ses autre fonctionnalités intéressantes est tout simplement le
fait que l'on peux ajouter des options. Il peux donc aussi servir de
gestion d'option pour son application.

Pour l'utiliser rien de plus simple. Il suffit de mettre votre script
dans un block `Dante.run`

```ruby
Dante.run('myapp') do |opts|
  myapp.run
end
```

Je suis vraiment impressionné par ce gem qui est issu de la société
[gomiso](http://gomiso.com). Merci à elle de nous permettre d'avoir
d'aussi bon gem.

[English Translation](http://blog-en.shingara.fr/dante-lets-you-put-some-daemon-in-your-code.html)
