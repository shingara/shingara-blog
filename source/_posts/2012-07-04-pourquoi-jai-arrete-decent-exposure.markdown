---
layout: post
title: "pourquoi j'ai arrêté decent_exposure"
date: 2012-07-04 09:49
comments: true
categories:
  - ruby
  - rails
  - decent_exposure
---
A la sortie de
[decent_exposure](https://github.com/voxdolo/decent_exposure), j'ai été
très enthousiaste. J'utilisais avant
[inherited_ressources](https://github.com/josevalim/inherited_resources/), mais je
commençais à découvrir les problèmes de `inherited_resources`. La
personnalisation de son controller peut vite devenir compliqué et peux
lisible si on sort du cadre d'un controller qui soit plus qu'un CRUD
simple. On finissait par avoir un code moyennement
DRY et les test étaient loin d'être évident car impossible coté controller
( limitation indiqué dans le README lui-même ).

Le grand avantage que j'ai vu dans `decent_exposure` est la facilité
d'accès des variables dans les vues. Plus besoin de faire appel à des
instances. On appelait simplement l'objet. Si celui-ci n'était pas
instancié alors `decent_exposure` le faisait pour nous. On obtenait ainsi un système beaucoup plus souple qui ne
chargeait les objets qu'au moment ou il était nécessaire. Un eager
loading intégré permettait d'éviter de rechercher en base plusieurs fois
le même objet. On pouvait aussi réutiliser plusieurs fois la même vue.
plus besoin de faire des assignations dans certaine action pour être sur
que l'objet existe. Il suffit juste d'exposer l'objet. Après on
l'utilise ou pas. De même l'utilisation des partials devient plus aisé
car, on peux surcharger facilement des variables par l'usage des
`locals`. J'y ai vu beaucoup d'intérêts.

Mais finalement cet intérêt n'existe que par l'usage de la méthode de
class d'un controller `helper_method`. Si on crée soit même sa propre
méthode avec son eager loading `decent_exposure` devient inutile.

`decent_exposure` a été pour moi un révélateur de l'utilité de la
méthode `helper_method`. Mais à l'usage, je trouve qu'il fait
finalement trop de chose caché si on utilise la méthode d'appel `expose`
sans bloc. Je me suis ainsi retrouvé à faire des `update_attributes` 2
fois sur mon objet. Un dans le `decent_exposure` l'autre dans mon
action. Forcement ça posait quelques soucis. Quand on sait que cela peut
arriver, pas de soucis. Mais c'est toujours embêtant de perdre quelques
heures à débugger le problème quand on le voit. Sans compter les fois ou
on ne le voit pas.

Une des limitations de `decent_exposure` selon moi est l'override de
cette méthode. Parfois pour éviter les `if` infini dans cette méthode il
est plus judicieux de définir directement la valeur de retour de cette
méthode dans sa méthode d'action. Cela permet de savoir exactement ce
que retourne cette méthode, mais aussi d'éviter les conflits dans
d'autre action.

Pour réalisé cela ma méthode stocke en mémoisation la variable
d'instance égale au nom de la méthode. Ainsi ma méthode ressemble à ceci
:

```ruby
def post
  @post ||= Post.find(params[:id])
end
helper_method :post
```

Si je souhaite donc avoir un nouveau Post retourné par cette méthode
j'ai juste à faire :

```ruby
def new
  @post = Post.new
end
```

Je surcharge le retour de la méthode post par la valeur `Post.new`. Pour
faire cela avec `decent_exposure` il faut surchargé la variable
`@_resource[:post]`. Mais rien ne m'assure que ce nom de variable d'instance
soit conservé dans les futurs version de `decent_exposure`. Une solution serait peut-être de créer une
méthode `post=()` lors de l'exposition d'un `post`, mais cela devient
peut-être moins pratique.

Voilà donc les raisons de mon arrêt d'utilisation de `decent_exposure`.
Je peux très facilement émuler le comportement de `decent_exposure` et
tout ça sans aucune dépendance externe.
