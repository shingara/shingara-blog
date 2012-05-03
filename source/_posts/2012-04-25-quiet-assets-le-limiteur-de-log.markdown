---
layout: post
title: "Quiet Assets le gem qui limite les logs"
date: 2012-04-25 09:42
comments: true
categories:
  - rails
  - assets_pipeline
  - gem
---

Voici une semaine, j'ai découvert un gem vraiment pratique.
[quiet_assets](https://github.com/evrone/quiet_assets).

Ce gems ne fait pas grand chose, mais a de grand avantage quand on
développe une application Rails (> 3.1) avec des assets en utilisant la
technologie 'asset_pipeline'.

Si c'est votre cas, vous vous êtes déjà rendu compte que chaques requêtes faites
à votre application pour obtenir vos assets est logués dans votre
fichier de log ( development.log en environement de dévelopment ). Des
requêtes comme celle-ci :

```
Started GET "/assets/application.js?body=1" for 127.0.0.1 at 2012-02-13 13:24:04 +0400
Served asset /application.js - 304 Not Modified (8ms)
```

On se retrouve ainsi à devoir remonter dans son fichier de logs pour
obtenir les logs qui nous interresse vraiment ( les log de la requête
courante ). Notre fichier de log est
vraiment pollué par ces assets si vous en avez comme moi beaucoup.


C'est là que [quiet_assets](https://github.com/evrone/quiet_assets)
devient vraiment interressant et pratique. Vous ajoutez ce gem dans
votre Gemfile au niveau du groupe 'development' et tous vos logs d'assets
disparaissent. Ils ne sont tous simplement plus logguées dans votre fichier
de log. On retrouve ainsi un fichier de log rapide a consulter et moins
pollué par des informations inutiles.

Comme quoi de tout petits gems peuvent nous rendre de grand service.

*Edit du 27 Avril 2012*

On pourrait se dire que cela pourrait être directement integré à Rails.
Hélas cela à été fait en partie, mais pas complétement.

Depuis la version 3.2.x et l'[issue 2639](https://github.com/rails/rails/issues/2639)
nous pouvons configurer le logger de Sprockets. Mais il n'est pas seul à
afficher les informations. Actionpack log toujours les requêtes faites
pour obtenir ces assets. Vous ne pourrez donc pas avoir un fichier de
log complétement épuré.

[English translation](http://blog-en.shingara.fr/quiet-assets-help-you-to-have-little-log.html)
