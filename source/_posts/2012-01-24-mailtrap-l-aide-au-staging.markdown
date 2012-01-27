---
layout: post
title: "mailtrap-l-aide-au-staging"
date: 2012-01-24 22:17
comments: true
category: administration
tags:
  - administration
  - smtp
  - mail
  - staging
published:false
---

Avant chaque mise en production d'un projet, il faut le test dans un
environement de staging. Pour avoir un environement le plus proche de
notre environement de production, une pratique courante est d'éffectuer
un dump de la base de production pour avoir un jeu de donnée complet et
valide. Ça nous permet aussi de vérifier que nos migrations ne posent
pas de problème, tester quelques pages au niveau de leur performance.

En gros faire plein de test comme si on jouait avec la prod mais sans
toucher au donnée de production.

Le plus gros soucis que l'on rencontre et beaucoup d'entre nous l'ont
rencontré, c'est l'envoi d'email. Tous les emails copié de la production
sont valide et posséde un utilisateur au bout. Si nous envoyons un email
réellement cela peut vite être problèmatique. Les solutions utilisées
généralement sont les suivantes.

## Remplacement de tous les emails de la base de donnée.

Une pratique simple est courant est la tâche qui remplace tous les
emails de la base de donnée par des emails possédés par la société.
Cette technique marche parfaitement, mais pose quelques problèmes :

* Il faut penser a toujours faire la modification après chaque copie de
  la base de production.
* Il faut mettre à jour son script pour remplacer tous les nouveaux
  champs qui posséde des emails si on en ajoute
* Il devient difficile de savoir à qui était réelement destiné l'email
  initial. On peux ainsi passer à coté de quelques bugs.

## Ne plus contacter de serveur SMTP et créer un fichier par email

Avec le framework Ruby on Rails, il est possible de définir que tous les
emails envoyés sont mis dans un fichier plutôt qu'envoyer par SMTP. Il
suffit de définir un nouveau smtp_delivery

<pre><code>
ActionMailer::Base.delivery_methods = :log
</code></pre>

Cette technique permet d'être sûr qu'aucun email n'est envoyé au client
finaux. L'inconvénient de cette technique est le stockage et la lecture
des emails. Tous les emails sont stocké sous forme de fichier
directement sur le serveur. Il peux donc être fastidieux de connaitre
les nouveaux emails envoyés, car ils sont classé par destinataire. On
peux ainsi ne pas constater qu'un email est envoyé sous certaine
conditions. De même, si votre application est utilisé par des personnes
n'ayant pas un accès shell à votre serveur, ils ne pourront pas lire les
mails envoyés.

## Le mock SMTP

Cette technique est selon moi la meilleure actuellement. Elle consiste
tout simplement à emuler un vrai serveur SMTP. Par contre, ce serveur ne
transmet pas les emails qu'il reçoit. Il les stocke et vous permet de
les consulter dans un client mail classique. Ils sont conservé
entièrement.

L'avantage est que n'importe qui peux consulter les emails générés, sans
aucune connaissance technique évolués et ils sont conservé entièrement.

A ma connaissance, il existe 3 produits qui permettent cela.

* [Mailcatcher](http://mailcatcher.me/)
* [Mailtrap.io](http://mailtrap.io/)
* [MockSMTP.app](http://mocksmtpapp.com/)

### Mailcatcher

C'est un produit open source tres simple d'utilisation. Vous télécharger
le gem, vous lancer la commande `mailcatcher` et c'est fini. Vous avez
ainsi un serveur SMTP sur le port 1025 et un client mail sur le port
1080.

Je vous le conseille dans un environement de développement. Il a en plus
quelques petits addons comme la notification growl.

Le seul soucis que j'ai rencontré avec est le deployement dans un
environement de staging. la commande `mailcatcher` ne permet pas
d'écouter sur toutes les interfaces. Il faut donc mettre un proxy devant
pour le rendre accessible à tout le monde. On doit donc le maintenir et
le gérer. Cela peux être un peu compliqué juste pour un simple mock.
Bien sûr rien ne vous empêche de le faire.


### Mailtrap.io

Voici un projet que j'ai découvert que très récement. Le concept est
exactement le même que celui de mailcatcher, sauf que vous n'avez plus à
gérer l'hébergement vous même. Tout est géré par
[railsware](http://railsware.com/), l'éditeur de ce service.

Vous créer un compte sur leur site, vous créer un serveur SMTP fictif et
c'est fini.

Vous obtenez les informations de connection à ce SMTP. Une fois que vous
définissez ces paramètres dans votre application, tous les emails seront
envoyé directement à Mailtrap.

Une gestion de collaboration sur les mailbox vous permet d'ajouter vos
collaborateur à cette mailbox pour qu'ils puissent voir les emails.

Cette solution est vraiment simple a mettre en oeuvre et très pratique.
Petit plus, c'est encore gratuit actuellement. Donc profitez en.
