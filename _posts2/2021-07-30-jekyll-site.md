---
title: About This Site
excerpt: >-
    How I created this site and how you can reproduce it for yourself
authors: C. García-Quirós
date: '2021-07-30'
layout: post
---

## Build webpages locally with jekyll
Fork the repo [ceciliogq.github.io](https://github.com/Ceciliogq/ceciliogq.github.io).

When using a fresh ubuntu installation I did not have any problem with ruby/bundle/jekyll versions. Following the instructions [here](https://jekyllrb.com/docs/installation/ubuntu/) should work.The previous time I had to tried different versions and had to modify the ruby version in the Gemfile.lock file to make it work.

1. `sudo apt-get install ruby-full build-essentail zlib1g-dev`
2. ```bash
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```
This code sets the installation folder of gems. I modified it to `$HOME/Utilites/gems` and since now the .bashrc is in the dotfiles it does not need to be added if we use a new machine.
3. `gem install jekyll bundler`
4. At this point, executing `jekyll` would throw an error. Since we already have a Gemfile in the github.io repo we do `bundle update` to update gem versions (or `bundle install` to install dependencies from Gemfile). You may also need to do what the warning message says when running jekyll about using the same bundler version than the lockfile.
5. To generate the html pages do `bundle exec jekyll serve` and open link from terminal or refresh it in the web browser.

## Publish webpage on github pages

1. First we commit the changes to the master branch in the github.io repo.
2. To make the changes available in Stackbit.com editor run the alias [`topreview`](https://github.com/Ceciliogq/dotfiles/blob/main/.bash_aliases) which add the changes to a "preview" branch that is used by Stackbit.
3. To actually deploy the changes to github pages run the alias [`deploy`](https://github.com/Ceciliogq/dotfiles/blob/main/.bash_aliases). This will enter in `_site` which contains the static html pages and will push to the gh-pages branch. `_site` is itself a git repo, where github pages runs jekyll.

**First time setup**. The setup procedure is kindly explained by [Scripted Tea](https://scriptedtea.com/tech/how-to-deploy-jekyll-to-github/). A basic requirement is that to host the webpage you need to create a repo whose name is `your-username.github.io`. Then in Settings->Pages you can set the branch you want to host, in our case `gh-pages` which contains the html pages.

**Warning:** it takes some time to propagate the changes to the github.io page since githubpages needs to build the site. Refresh browser with `Ctrl+Shift+R`.
