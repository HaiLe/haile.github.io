# Problem with installing Jekyll on Mojave

Recently I ran into a problem install Jekyll with Mac Mojave. 

There was a bug reported https://github.com/jekyll/jekyll/issues/7274. 

Here are the steps that resolved the issue.

### install rbenv via brew 
`brew install rbenv ruby-build`

### Add rbenv to bash so that it loads every time you open a terminal

`echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile`

`source ~/.bash_profile`


### Install Ruby through rbenv and set as default

`rbenv install 2.6.3`

`rbenv global 2.6.3`

### Install Jekyll

`gem install bundler jekyll`






