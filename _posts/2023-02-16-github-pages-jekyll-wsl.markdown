---
layout: post
title:  "GitHub Pages + Jekyll + WSL on Windows 11"
date:   2023-02-16 10:46:57 -0500
categories: stuff-and-things
tags: 
---
I attempted to follow the instructions and process verbatim in the GitHub Pages documentation while setting up this Jekyll site. Part of the instructions send you to the Jekyll site to setup Ruby / RubyGems / Bundler / Jekyll. I happened to be working on my Windows machine at the time and didn't feel like dealing with Ruby + DevKit + Windows headaches, so I opted for doing this all inside Windows Subsystem for Linux w/ Ubuntu.

Even though I used the instructions for Windows Subsystem for Linux, the recommended BrightBox PPA repository for the packages kept returning 404s, which makes it hard to install the packages :)

I opted instead to just use the [Ubuntu install documentation](https://jekyllrb.com/docs/installation/ubuntu/) since Ubuntu under WSL is pretty damn close to the full-blown real version at this point anyway.

# Install Ruby / Deps / Jekyll

```bash
sudo apt-get install ruby-full build-essential zlib1g-dev gh

echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

gem install jekyll bundler
```

# Clone repo and create Jekyll files

```bash
gh auth login
gh repo clone justingarfield.github.io
cd justingarfield.github.io

jekyll new --skip-bundle .
```

# Make GitHub Pages configuration changes

```bash
sed -i '/^[^#]/ s/\(^.*gem "jekyll", "~> .*$\)/#\ \1/' Gemfile

# See https://pages.github.com/versions/ for github-pages gem version (currently version 228)
sed -i 's/.*# gem "github-pages", group: :jekyll_plugins.*/gem "github-pages", "~> 228", group: :jekyll_plugins/' Gemfile
```

# Install Required Dependencies

```bash
bundle install
bundle add webrick
```

# Locally serve your GitHub Pages site

```bash
bundle exec jekyll serve --livereload
```

Navigate to: [http://127.0.0.1:4000/](http://127.0.0.1:4000/)

# Upgrade Gems over-time

Every now and then, you should run the command below to update your dependencies. If they get too out-of-date, you may have issues getting GitHub to build your stuff.

```bash
bundle update github-pages
```
