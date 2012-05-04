---
layout: post
title: "moped le nouveau driver mongodb pour ruby"
date: 2012-04-30 09:42
comments: true
categories:
  - ruby
  - mongodb
---

Depuis décembre 2012, [Bernerd Schaefer](https://github.com/bernerdschaefer) a commencé a créer un
driver MongoDB pour ruby. Ce nouveau driver MongoDB n'a pas été crée par
10Gen (créateur de MongoDB) contrairement au gem
[mongo-ruby-driver](https://github.com/mongodb/mongo-ruby-driver).

# Le but de ce gem

Ce gem a été crée suite aux frustrations de l'équipe de développement de
Mongoid qui a fait plusieurs fois des propositions de changement de
design du driver officiel. Tous changement de design du driver a été
refusé.

## Thread-safe

Un des buts de ce gem est de permettre d'avoir un gem qui est le plus
possible thread-safe. Le driver officiel mongoDB est parfois considéré
comme non thread-safe notament par [Mike Perham](https://github.com/mperham/sidekiq/wiki/Problems-and-Troubleshooting).
C'est un vrai problème alors que la communauté ruby
commence enfin à redécouvrir les threads. Cet état empêche par exemple
l'usage de [sidekiq](http://mperham.github.com/sidekiq/) avec MongoDB.

## Suppression de l'extension

Avec le driver MongoDB officiel, il est très fortement conseillé
d'installer le gem [bson_ext](http://rubygems.org/gems/bson_ext) sans
celui-ci les performances sont beaucoup moins bonnes. Par contre cette
extension étant écrite en C, elle est incompatible avec JRuby. Il a
fallu créer une version JRuby du gem bson. Ça rend la maintenabilité
plus complexe.

Dans le cas de moped, aucune extension C n'est à prévoir.
Il est naturellement plus performant. Il arriverait même à être plus
performant que l'extension C selon
les benchmarks réalisés pour le projet.

Seul ombre au tableau, la génération d'un ObjectId est plus lente avec
moped qu'avec le gem officiel bson selon un [thread de la mailing-list de Mongoid](https://groups.google.com/d/topic/mongoid/87IdIKO8-VM/discussion).
Mais cela pourra être améliorer par la suite, je n'en doute pas.

## Une API plus simple

L'API de moped est vraiment plus belle que celle du gem mongo. Ce
changement est bien sûr subjectif. Elle est beaucoup plus orienté ruby.
Elle permet aussi la combinaison de recherche/mise à jour.

## Meilleure gestion du replicat Set

Une des fonctionnalités principale de MongoDB est la gestion du
replicatSet. Avec Moped, cette gestion devient vraiment plus souple et
surtout ne lève plus d'exception la première fois que le node maître est
down. Cette fois ci le basculement se fait automatiquement.

Plus besoin de passer la liste de vos nodes en configuration. Moped
utilise directement les mécanismes de MongoDB pour découvrir les
nouveaux nodes et ainsi les utiliser.


# Les limitations

Par contre, moped a certaine limitation.

## Ruby 1.9 uniquement

Moped n'est pas compatible avec Ruby < 1.9. Le choix de l'implémentation
a été fait de ne pas être compatible avec les plus anciennes version de
ruby.

Personnellement, je suis d'accord avec ce choix, car l'usage de ruby 1.8
doit disparaitre.

## Pas de support de GridFS

GridFS n'est pas supporté nativement dans moped. Ce choix a été fait
pour limiter le core du gem. Bien sûr rien n'empêche de créer une
extension de moped gérant GridFS.

# Intégration dans Mongoid 3

Mongoid 3.x utilisera désormais moped à la place du driver mongodb de
10Gen. Si vous souhaitez donc commencer un peu à l'utiliser essayer
cette nouvelle version majeur de Mongoid.
