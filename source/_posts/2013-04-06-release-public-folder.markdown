---
layout: post
title: "Release de public folder"
date: 2013-04-06 09:49
comments: true
categories:
  - ruby
  - rails
  - public_folder
---
Depuis maintenant 1 semaine, je suis à la recherche d'un nouvel emploi.
Durant ma recherche d'emploi, on m'a fait une demande d'exercice en
Ruby. Étant un convaincu de l'open source et étant globalement fier de
ce que je peux produire, j'ai décidé de le sortir en open source.

Voici donc [Public Folder](http://github.com/shingara/public_folder)

Ce projet est extrêment simple, il s'agit d'une interface de gestion de
répertoires. À partir de la racine, on peut ainsi naviguer à travers une
arborescence que l'on peut créer soit même. On peut donc créer des
répertoires et uploader des fichiers directement dans l'application.

Le stockage des fichiers se fait directement sur disque dans la même
arborescence que celle définie dans l'application. Elle est juste comme
git, si vous créez un dossier dans l'application et qu'il ne contient
aucun fichier, le dossier ne sera pas créé sur le disque.

Au niveau de la navigation, l'url correspond à l'architecture du dossier
et on peut trier chaque liste par date/type/poids ou noms.

Pour réaliser cette application, la stack est full Rails, soit
ActionPack et ActiveRecord. J'ai juste utilisé `decent_exposure`,
`simple_form` et `draper` comme gems. Je ne peux plus du tout me passer
d'eux tellement ils sont devenus essentiel pour moi.

Bien évidement, ce projet est complétement testé. J'ai une couverture de
test de 99% selon
[Coveralls](https://coveralls.io/r/shingara/public_folder). Il a une note de 4
sur [CodeClimate](http://codeclimate.com/github/shingara/public_folder). Vous pouvez le voir aussi sur [Travis](https://travis-ci.org/shingara/public_folder).

Je ne compte pas continuer le développement de cette application. Je
n'en ai strictement aucun besoin. Par contre, je la laisse à la
postérité et aux lecteurs de code en tout genre.
