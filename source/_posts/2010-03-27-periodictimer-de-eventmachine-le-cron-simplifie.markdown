---
layout: post
title: PeriodicTimer de EventMachine, le cron simplifié
typo_id: 222
comments: true
---
[EventMachine](http://rubyeventmachine.com/) est une des meilleures librairies Ruby,
tout en étant une des plus sous-estimées et mal connues.

J'ai donc commencé à regarder un peu dans quels cas utiliser EventMachine.
La première application que j'en ai trouvé c'est tout simplement le [PeriodicTimer](http://eventmachine.rubyforge.org/EventMachine/PeriodicTimer.html).

Son utilisation la plus simple est le remplacement d'une cronjob.


Je m'explique : une cronjob est vraiment une très bonne chose. On peut définir très précisément l'heure à laquelle on souhaite qu'une tâche s'exécute. Celle-ci s'exécute alors à ce moment précis. Cron est vraiment un outil magnifique. Cependant, dans
le cas d'une tâche récurrente qui doit être effectuée régulièrement, l'utilisation d'un cronjob peut avoir un gros inconvénient.

Cron n'a aucune gestion de queue. Ainsi on peut se retrouver très simplement avec une quantité astronomique de tâches essayant de s'exécuter en même temps. Votre « load » explose et il faut redémarrer votre serveur. Bien sûr cela n'arrive que si vous lancez une tâche plus fréquemment que son temps d'exécution.

La seule solution serait d'avoir une petite application - comme : [jobq](http://forge.bearstech.com/trac/wiki/JobQueue) - qui gére pour vous un système de queue. Bien sûr, si le fait d'avoir une queue ne sert strictement à rien, cette solution peut être un peu overkill.

L'avantage de PeriodicTimer est que les tâches sont exécutées après un temps défini. Il n'y a aucune gestion d'heure de lancement. Il y a juste un lancement de la tâche après X secondes une fois que la précédente tâche est finie et qu'aucune autre tâche ne soit en cours d'exécution. Cela entraîne effectivement un glissement progressif de l'heure de lancement. Mais globalement, ça ne pose aucun problème. L'important est que la tâche soit exécutée régulièrement. C'est le cas, par exemple dans le cas de récupération de statistique.



Voici [un exemple de code](http://gist.github.com/345000) qui utilise
PeriodicTimer

``` ruby
require 'eventmachine'
require 'timeout'
EventMachine.run {
 EventMachine::PeriodicTimer.new(10) do
   puts "#{Time.now} : I am 10"
   sleep 10
 end

 EventMachine::PeriodicTimer.new(1) do
   puts Time.now
 end
}
```

Voici la sortie qui est effectuée si on lance l'application pendant 50s.

```
$ ruby em_periodic.rb
Fri Mar 26 16:07:39 +0100 2010
Fri Mar 26 16:07:40 +0100 2010
Fri Mar 26 16:07:41 +0100 2010
Fri Mar 26 16:07:42 +0100 2010
Fri Mar 26 16:07:43 +0100 2010
Fri Mar 26 16:07:44 +0100 2010
Fri Mar 26 16:07:45 +0100 2010
Fri Mar 26 16:07:46 +0100 2010
Fri Mar 26 16:07:47 +0100 2010
Fri Mar 26 16:07:48 +0100 2010 : I am 10
Fri Mar 26 16:07:58 +0100 2010
Fri Mar 26 16:07:59 +0100 2010
Fri Mar 26 16:08:00 +0100 2010
Fri Mar 26 16:08:01 +0100 2010
Fri Mar 26 16:08:02 +0100 2010
Fri Mar 26 16:08:03 +0100 2010
Fri Mar 26 16:08:04 +0100 2010
Fri Mar 26 16:08:05 +0100 2010
Fri Mar 26 16:08:06 +0100 2010
Fri Mar 26 16:08:08 +0100 2010
Fri Mar 26 16:08:08 +0100 2010 : I am 10
Fri Mar 26 16:08:18 +0100 2010
Fri Mar 26 16:08:19 +0100 2010
Fri Mar 26 16:08:20 +0100 2010
Fri Mar 26 16:08:21 +0100 2010
Fri Mar 26 16:08:22 +0100 2010
Fri Mar 26 16:08:23 +0100 2010
Fri Mar 26 16:08:25 +0100 2010
Fri Mar 26 16:08:26 +0100 2010
Fri Mar 26 16:08:27 +0100 2010
Fri Mar 26 16:08:28 +0100 2010
Fri Mar 26 16:08:28 +0100 2010 : I am 10
```

On peut constater que la tâche qui doit s'exécuter toutes les
secondes le fait bien. Par contre la tâche qui s'exécute au bout de 10
secondes effectue un lock sur le thread. Ainsi la tâche qui s'exécute toutes
les secondes attend que le lock soit libéré. Une fois terminée,
l'exécution de la tâche toutes les secondes recommence. La tache qui doit s'exécuter toutes les 10
secondes attendra, quand à elle, 10 nouvelles secondes après avoir été terminée.

C'est là une grande puissance de PeriodicTimer. On a facilement une succession
de tâche qui ne se marchent jamais sur les pieds. Désormais,  je n'utilise plus que

cette solution pour créer des scripts de récupération de données.
