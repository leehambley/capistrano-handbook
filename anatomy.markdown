---
title: Anatomy of a Capistrano Installation
layout: default
---

Capistrano, not unlike rake, relies on you having a `Capfile` in your project root, this name is case insensitive, and is typically title-cased for historic reasons.

Capistrano expects you to have a `config` directory into which it will install a `deploy.rb` file that contains your settings, if you do not have a config directory, one will be created for you.

The `Capfile` and `deploy.rb` file is loaded when you call the `cap` executable - in the event that you aren't in the root of your application (or more accurately that there isn't a `capfile`) in your present working directory, `cap` will search up the directory structure until it finds one, *this may include your home directory*.

**Beware of this when dealing with multiple projects, or nested projects - this feature was intended so that you could run a deploy, or open a [`cap shell`](http://weblog.jamisbuck.org/2006/9/21/introducing-the-capistrano-shell) without moving to the root of your application.**

Typically *capifying* an application will create something akin to the following in `config/deploy.rb`

    set :application, "set your application name here"
    set :repository,  "set your repository location here"

    # If you aren't deploying to /u/apps/#{application} on the target
    # servers (which is the default), you can specify the actual location
    # via the :deploy_to variable:
    # set :deploy_to, "/var/www/#{application}"

    # If you aren't using Subversion to manage your source code, specify
    # your SCM below:
    # set :scm, :subversion

    role :app, "your app-server here"
    role :web, "your web-server here"
    role :db,  "your db-server here", :primary => true

If your application is not separated into `application`, `web` and `database` servers, you can either set them to be the same value; or comment out, or remove the one you do not require.

Typically for a `PHP` application one would expect to comment out the `web` or `app` roles, depending on your requirements. Certain built in tasks expect to run only on one, or the other kind of server.