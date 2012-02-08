---
layout: post
title: "Localize avec mongoid"
date: 2012-02-06 22:02
comments: true
categories:
- mongoid
- i18n
- translate
published: true
---

Depuis la version 2.4.0 de [Mongoid](http://mongoid.org), une nouvelle
fonctionnalité à été ajoutée très discrétement. C'est tout simplement la
gestion native des traductions en base.

La [documentation compléte](http://mongoid.org/docs/documents/localized.html) de cette fonctionnalité est sur le site de
[mongoid](http://mongoid.org).

Grâce à cette fonctionnalité, on peut définir tout simplement qu'un
champs de Mongoid est localizable. La gestion de la locale est faite de
manière très intelligente et surtout en complète coordination avec
MongoDB.

Contrairement à une gestion de traduction dans une Base de donnée
relationnelle, qui créé une table de liaison, Mongoid stocke un hash
avec la locale comme clé et la traduction comme valeur.

Mongoid n'a plus qu'à faire la glue entre le système pour que les accès
en lecture/écriture en fonction de la locale et la recherche
