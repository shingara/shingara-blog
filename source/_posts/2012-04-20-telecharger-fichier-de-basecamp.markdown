---
layout: post
title: "Récupération de ses fichiers sur basecamp"
date: 2012-04-20 09:38
comments: true
categories:
  - script
  - ruby
  - basecamp
---

Au niveau de mon travail actuel chez [Be My Boat](http://bemyboat.com),
nous sommes actuellement en train de migrer de
[Basecamp](http://basecamphq.com) vers
[Trello](http://trello.com).

Pour éviter de perdre toutes les informations qui ont été mis dans
basecamp, nous avons réalisé un import complet. Par contre, l'import ne
comprend pas les fichiers qui ont pu être ajouté dans basecamp.

Pour ne pas perdre ces fichiers qui font parti du patrimoine de
l'entreprise, j'ai créé un petit script pour télécharger tous ces
fichiers.

Je vous le partage si vous en avez besoin un jour.

{% gist 2420838 %}

Dans ce script j'utilise
[Faraday](https://github.com/technoweenie/faraday) pour faire le requêtage avec l'API et
aussi télécharger les fichiers. Je suis personnellement un grand fan de
Faraday. C'est selon moi la meilleure librairie de requêtage HTTP
actuelle du monde Ruby. Utilisez la dès que vous avez besoin de faire du
requêtage HTTP.
