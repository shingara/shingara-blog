---
layout: post
title: "tester ses vues rabl"
date: 2012-06-28 09:40
comments: true
categories:
  - test
  - rspec
  - rabl
---
Personnelement, quand je fais des API, j'utilise le gem
[Rabl](https://github.com/nesquena/rabl). Je trouve ce gems vraiment
génial. Avant son utilisation, je faisais mes API json avec la méthode
`#to_json`. Mais pour ceux qui l'on utilisé, vous savez que c'est un
enfer à maintenir. Surtout si vous avez plusieurs type de représentation
de votre objet à différent endroit.

# Rabl la simplicité d'expression

Pour gérer ses vues en JSON ou XML, les gems classique sont `JBuilder`
et `XmlBuilder`. Deux gems réalisées par DHH. Mais, personnelement, je
les trouves assez verbeuses comme vues. A contrario, Rabl a une syntaxe
trés simplifiée. On peux ausi avoir un système de partial qui permet une
réutilisation compléte. Petit bonus par rapport à JBuilder/XmlBuilder
est qu'il utilise la même vue pour générer du XML ou du JSON. Donc pas
besoin de faire plusieurs vues en fonction du format de sortie.

Voici un example de vue Rabl :

```ruby
object @post
attribute :content
child(:author) do
  attribute :firstname, :lastname
end
```

# Tester sa vue Rabl

Pour tester le résultat de cette vue Rabl, deux possibilités s'offre à
nous. Un test de la génération de la vue par un test d'intégration
couplé avec le test du controller, soit un test de la vue. Finalement
tester une vue Rabl c'est ni plus ni moins que tester un retour de la
méthode `#to_json`. Donc si nous testons notre vue directement à partir
des données passées, c'est plus rapide et plus unitaire.

Depuis la version 0.6.0 de Rabl, une méthode permet de générer la vue
directement. C'est cela que nous allons utiliser pour faire notre test
de vue.

Voici donc un test de notre vue ( défini dans
`spec/views/posts/show_rabl_spec.rb` )

```ruby
require 'spec_helper'

describe "blogs/show.rabl" do

    let(:auhor) { Author.new(:firstname => 'Cyril', :lastname => 'Mougel') }
    let(:blog) { Blog.new(:content => 'hello', :author => author) }
    let(:valid_json) {
      {
        :content => blog.content,
        :author => {
          :firstname => author.firstname,
          :lastname => author.lastname
        }
      }.to_json
    }

    let(:render) {
      Rabl.render(
        blog,
        'blog/show',
        :view_path => 'app/views'
      )
    }

    it 'should render valid_json' do
      render.should eql(valid_json)
    end

end
```

On obtient ainsi un test unitaire vraiment simple et une validation
compléte du json obtenu. Le test est vraiment très rapide et n'est pas
pollué par le test du controller.

J'ai par contre découvert des limitations à ce test car, on ne peux
passer à la méthode `#render` que les objects définis par `object` ou
`collection`. Si on veux tester une vue rabl qui utilise plusieurs
variables, la tâche devient très hardu. J'ai donc commencé à proposer un
[Pull Request](https://github.com/nesquena/rabl/pull/261) pour passer directement des variables dans sa vue Rabl, de
la même méthode que les partials avec l'options `:locals`. Vous pouvez
la [suivre sur github](https://github.com/nesquena/rabl/pull/261)
