---
layout: post
title: Oupsnow 0.5.0 est sortie
typo_id: 219
---
Ca y est, j'ai presque pris un cycle de release pas trop mauvais. Ainsi après seulement un mois après la version 0.4.1 de [Oupsnow](http://oupsnow.rubyforge.org), voici la version 0.5.0. Cette version apporte quelque feature, mais marque surtout un moment de stabilité dans le code.

## Les nouveautés

 * Ajout d'un filtre sur la recherche des tickets pour ne voir que les tickets fermé ou non
 * Possibilité d'éditer une milestone pour les admin d'un projet
 * Possibilité de définir une milestone comme actuelle. Par défaut, c'est la première milestone créée
 * Possibilité de récupérer son password par email
 * Possibilité de rester connecter avec un remember_me
 * Ajout d'information concernant le nombre de tickets filtrés ou vues
 * Possibilité pour chaque utilisateur loggé de suivre un ticket. Un utilisateur qui suit un ticket recevra ainsi à chaque mise à jour de ce ticket un email concernant cette modification.
 * Les utilisateurs ne peuvent plus changer leur email.

Enfin comme d'habitude voici mon fichier capistrano de déployement pour faciliter celui-ci.

## Ficher de déployement capistrano


<typo:code lang="ruby">
set :application, "oupsnow"
set :repository,  "git://github.com/shingara/oupsnow.git"
set :domain, "dev.shingara.fr"

# If you aren't deploying to /u/apps/#{application} on the target
# servers (which is the default), you can specify the actual location
# via the :deploy_to variable:
set :deploy_to, "XXXXXXXXX"

# If you aren't using Subversion to manage your source code, specify
# your SCM below:
# set :scm, :subversion
set :scm, :git
set :git_enable_submodules, 1

set :runner, "xxxx"
set :user, "xxxx"
set :use_sudo, false

set :thin_conf, "/etc/thin/#{domain}.yml"

set :rails_env, "production"                                                                                                                                   

role :app, domain
role :web, domain
role :db,  domain, :primary => true

task :update_config, :roles => [:app] do
  run "ln -s #{shared_path}/config/database.yml #{release_path}/config/database.yml"
  run "ln -s #{shared_path}/config/email.yml #{release_path}/config/email.yml"
  run "ln -s #{shared_path}/config/initializers/errornot.rb #{release_path}/config/initializers/errornot.rb"
  run "cd #{release_path} && echo 'GOOGLE_ANALYTICS=\"XXXXXXXX\"' >> config/environment.rb"
end

namespace :deploy do
  task :start, :roles => [:app] do
    run "thin -C #{thin_conf} start"
  end 

  task :stop, :roles => [:app] do
    run "thin -C #{thin_conf} stop"
  end 

  task :restart, :roles => [:app] do
    run "thin -C #{thin_conf} restart"
  end 
end

task :update_db do
  run "cd #{current_path} && RAILS_ENV=#{rails_env} rake db:update"
end

after "deploy:update_code", :update_config
after "deploy:symlink", :update_db
</typo:code>

[English translation](http://blog.shingara.fr/en/2010-03-13-oupsnow-0-5-0-released.html)
