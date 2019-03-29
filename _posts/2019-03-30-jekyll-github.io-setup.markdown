---
layout: post
title:  "How to setup (and develop) a Github.io page using Jekyll (Linux)"
date:   2019-03-29 08:27:00 +0100
categories: jekyll github.io
---

Refer to [help page on github][github-help]

Short guide:

- Check if you have, or install, Ruby > 2.1.0 + Gem

- Install `bundler` and `jekyll` if necessary:

```
$ sudo gem install bundler jekyll
...
$ _
```

- Initialize and empty directory, enters it and create a `Gemfile` with the following content:

```
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
```

- Downloads necessary gems:

```
$ bundle install
....
$ _
```

- Initialize an empy jekyll site on a new directory:

```
$ bundle exec jekyll MY_JEKYLL_DIR
...
$ cd MY_JEKYLL_DIR
$ _
```

- Change the generated `Gemfile` in the following way:
   - comment the line that starts with `gem "jekyll", ...`
   - uncomment the line that starts with `gem "github-pages",...`

- Initialize a git repo in the directory:

```
$ git init
...
$ git add .
...
$ git commit -m "initialized jekyll site"
...
$ _
```

- Connect to the remote repository on Github to host the pages and push the modification:

```
$ git remote add origin git@github.com:<username>/<username>.github.io.git
...
$ git push -u origin master
...
$ _
```

To test the site locally launch the following command:

```
$ bundle exec jekyll serve
...
```

and open the `http://localhost:4000` page to see the result.

To check the page online on Github pages, open the URL `https://<username>.github.io/`

[github-help]: https://help.github.com/en/articles/setting-up-your-github-pages-site-locally-with-jekyll