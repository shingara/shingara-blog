---
layout: post
title: "vundle ou le bundler de vim"
date: 2012-05-24 09:40
comments: true
categories:
  - vim
  - editeur
---
Personnellement, j'utilise vim comme éditeur de texte depuis mon
école. J'ai toujours apprécié vim et je ne compte toujours pas changer
d'éditeur.

# Mon évolution de la gestions des plugins vim.

Vim a une très grande quantité de plugins disponibles et il est très
rapidement indispensable d'en utiliser plusieurs. La gestion des plugins
est donc très importante. Au fil des années, j'ai changé de technique en
fonction des possibilités apportées par vim ou par les développeurs.

## Gestion manuelle

Au début, je gérais manuellement mes plugins en les ajoutant dans les
dossiers correspondant. Je versionnais mon dossier `.vim` et `.vimrc` et je pouvais le faire
évoluer globalement. Le soucis de cette technique est qu'il est pas
forcement évident de gérer les mises à jour des plugins et leur
installation n'est pas forcement simple car il faut mettre plusieurs
fichiers à plusieurs endroits.

## L'arrivée des vimballs

Dans une évolution de vim, il y a eu la possibilité de gérer des
vimballs. L'avantage de cette technique est qu'on avait moins de
problème lors de l'installation et on pouvait gérer un peu plus
facilement les mises à jour car on savait rapidement quelle version on
utilisait. Mais la encore il fallait allez régulièrement sur
vimscript.org
pour voir si nos plugins avaient changé de version. Chose que finalement
on faisait très peu au final.


## Gestion par git-submodule

Avec l'arrivée de github, les choses ont pu changer. Les plugins vim ont
commencé à avoir un dépôt centralisé, on pouvait suivre directement
l'évolution des scripts. Même vimscript a converti tous les plugins
qu'il hébergeait en repository git. Maintenant on peux gérer tous ses plugins
en faisant des mises à jour de repository git. J'ai ainsi commencé a gérer des
submodule et des liens symboliques vers ces submodules. La mise à jour
était automatique, mais l'installation devenait encore plus compliqué
qu'avant.

## Janus, la distribution vim

C'est alors que Yehuda Katz et Carl Lerche ont commencé à créer
[janus](https://github.com/carlhuda/janus). Ce projet se veut comme une
distribution de gestion de plugins vim. Il contient un jeu de tâche rake
qui permette de mettre à jour facilement ses plugins. Un `.vimrc` est aussi
fourni pour utiliser les plugins par défaut et ajouter une configuration
minimal pratique. Ce projet est vraiment génial pour commencer à
pratiquer vim. On peux ainsi avoir un jeu de plugins pratique et
intéressant. Par contre, la gestion des versions de janus avec ses
propres usage peux vite devenir compliquée. En gros c'est bien si on ne veut
jamais modifier sa conf vim.

## Pathogen, le premier plugin vim de gestions de ses plugins

Très peu de temps après [Tim Pope](https://github.com/tpope) ( très prolifique développeur de plugins
vim ) a sorti [Pathogen](https://github.com/tpope/vim-pathogen). Ce
plugin permet de gérer directement la gestion de ses plugins dans son vimrc. J'avoue ne pas avoir
trop regardé comment fonctionne ce plugin. On m'en a toute fois dit du
bien. Il y a eu pas mal de projet pour que janus utilise pathogen
typiquement.


## Vundle, le bundler de vim.

Mais c'est là qu'on m'a parlé du plugin qui me semble le meilleur
actuellement concernant la gestion de ses plugins vim,
[Vundle](https://github.com/gmarik/vundle). Comme pathogen, la gestion
des plugins est faite directement dans son vimrc. Son usage y est très
simplifié et pratique. Si vous connaissez
[Bundler](http://gembundler.org) alors vous comprendrez facilement sont
usage car il en est très inspirés.

# Utilisation de Vundle

## Installation de Vundle

Pour installer vundle, un simple clone du repository du projet et 2
lignes dans votre `.vimrc` suffisent.

```
git clone https://github.com/gmarik/vundle.git ~/.vim/bundle/vundle
```

```vim
set rtp+=~/.vim/bundle/vundle/
call vundle#rc()
```

## Configuration de ses plugins

Pour utiliser vundle une fois installé. C'est extrêmement simple. il
suffit d'utiliser la commande `Bundle` et le chemin de votre plugin.
Ainsi pour installer le plugin `vim-rails` il suffit juste de faire :

```vim
Bundle 'tpope/vim-rails.git'
```

## Installation de ses plugins

Ensuite, une fois votre vim lancé deux commandes vous suffisent.
`BundleInstall` qui permet d'installer tous les plugins que vous avez
définis et `BundleInstall!` qui permet de les mettre à jour.

Désormais ils ne vous reste plus qu'à trouver les plugins que vous
souhaitez et les utiliser.

# Mon vimrc

Si vous souhaitez découvrir mon usage de vim, [mon vimrc est ainsi
disponible sur github](https://github.com/shingara/vim-conf). Grâce à
Vundle rien de plus simple que de le lire et découvrir rapidement les
plugins vim que j'utilise.

[English Translation](http://blog-en.shingara.fr/vundle-the-bundler-of-vim.html)

