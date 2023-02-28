---
title: Configuring GitHub Pages and Travis CI
description: Configuring GitHub Pages and Travis CI
date: 2016-06-20
tags:
  - GitHub
  - Pages
  - Travis
  - CI
---

<img src="https://cdn.travis-ci.com/images/logos/TravisCI-Mascot-1-bc6a3179f3e8e1ab1456634bd55a448a.png" alt="Travis CI" width="200" style="display: block;margin-left: auto;margin-right: auto;" />

I'm new to [Travis CI].  Almost all of the CI experience I have is with [TeamCity] and [TFS].  So, I thought I would use this GitHub pages site as an excuse to play with setting up a new-to-me CI system.  I started out following the [GitHub help documentation] on the subject hoping to get the site to build.  Heh, small wins.  The exact steps I followed are: 

- Create a file named "Gemfile" in the root folder with the following content:

    > source 'https://rubygems.org'  
    > gem 'github-pages'

- Create a file named ".travis.yml" in the root folder with the contents:

    > language: ruby  
    > script: "bundle exec jekyll build"

- Setup Travis CI to "watch" your GitHub pages repo

After reading these instructions I'm thinking "Alright, that sounds pretty straightforward" but the build fails with the comment:

> An error occurred while installing public_suffix (1.5.3), and Bundler cannot continue.  
> Make sure that 'get install public_suffix -v '1.5.3' succeeds before bundling.

What.  Then I noticed this line a few lines up in the build log:

> Gem::InstallError: public_suffix requires Ruby version >= 2.0.

Ahhh I see.  Travis CI must be using a version of Ruby that is too old as part of it's default configuration.  So, how do we tell Travis to run a different version of Ruby?  Simple.  Add the following line to the .travis.yml file:

> rvm: 2.1.1

Done.  Build succeeds and life is grand.

Oh, and apparently I'm [not the only one that has had this issue].  In fact, this individual [spells it out quite nicely].

[Travis CI]: https://travis-ci.org/
[TeamCity]: https://www.jetbrains.com/teamcity/
[TFS]: https://www.visualstudio.com/products/tfs-overview-vs
[GitHub help documentation]: https://help.github.com/articles/viewing-jekyll-build-error-messages/#configuring-a-third-party-service-to-display-jekyll-build-error-messages
[not the only one that has had this issue]: https://github.com/github/pages-gem/issues/122
[spells it out quite nicely]: https://github.com/github/pages-gem/issues/122#issuecomment-158942037