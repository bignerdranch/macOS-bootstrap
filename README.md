Big Nerd Ranch iOS/Mac Machine Bootstrap Script
===============================================

![](https://travis-ci.com/bignerdranch/cocoa-machine-bootstrap.svg?token=mjyegwmpGK1tsHqNiqGk&branch=master)

Bootstrap script to get up and running developing iOS and Mac apps for Big Nerd Ranch

It can be run multiple times on the same machine safely.
It installs, upgrades, or skips packages
based on what is already installed on the machine.

What it sets up
---------------

Mac OS X tools:

* [Homebrew] for managing operating system libraries.

[Homebrew]: http://brew.sh/

Unix tools:

* [Git](https://git-scm.com/) for version control
* [OpenSSL](https://www.openssl.org/) for Transport Layer Security (TLS)

GitHub tools:

* [Hub](http://hub.github.com/) for interacting with the GitHub API

Ruby toolchain (this will be helpful for project Gems such as [Fastlane](https://fastlane.tools/) and [CocoaPods](https://cocoapods.org/):

* [Bundler](http://bundler.io/) for managing Ruby libraries
* [Rbenv](https://github.com/sstephenson/rbenv) for managing versions of Ruby
* [Ruby Build](https://github.com/sstephenson/ruby-build) for installing Rubies
* [Ruby](https://www.ruby-lang.org/en/) stable for writing general-purpose code

iOS and Mac tools:

* [Carthage](https://github.com/Carthage/Carthage) for managing Cocoa libraries

It should take less than 15 minutes to install (depends on your machine).

Requirements
------------

Shell:

* [z shell](http://zsh.sourceforge.net/)
* [bash](https://www.gnu.org/software/bash/)

Operating System:

* OS X Yosemite (10.10)
* OS X El Capitan (10.11)

Install
-------

Clone, review, then execute the script:

```bash
git clone git@github.com:bignerdranch/cocoa-machine-bootstrap.git
less repo_path/bootstrap
bash repo_path/bootstrap
```

Customize in bootstrap.local and Brewfile.local
---------------------------------------------------

### Brewfile.local

A [Brewfile](https://github.com/Homebrew/homebrew-bundle) is like a Gemfile for non-ruby dependencies. Anything you would install via [homebrew](http://brew.sh/) you can drop into your `Brewfile.local` and it will be installed during the bootstrap script.

An example Brewfile looks like this:
```ruby
tap 'homebrew/bundle'
tap 'thoughtbot/formulae'
tap 'kevwil/patches'

brew 'ack'
brew 'git'
brew 'carthage'
brew 'chisel'
brew 'openssl'
brew 'fish'
brew 'gifsicle'
brew 'go'
brew 'heroku'
brew 'irssi'
brew 'keybase'
brew 'leiningen'
brew 'mogenerator'
brew 'mutt'
brew 'node'
brew 'pandoc'
brew 'ruby-build'
brew 'rbenv'
brew 'redis'
brew 'the_silver_searcher'
brew 'tmux'
brew 'xcproj'
brew 'xctool'
brew 'rcm'	
``` 

### bootstrap.local

Your `bootstrap.local` is run at the end of the BNR Bootstrap script.
Put your customizations there.
For example here is my .local:

```bash
#!/bin/bash

readonly prefsLocalCheckoutPath="$HOME/app_prefs"

update_dotfiles() {
  fancy_echo "Updating dotfiles ..."

  local dotfilesLocalCheckoutPath="$HOME/dotfiles"
  local dotfilesRepoURL="git@github.com:rcedwards/dotfiles.git"

  if [ -r "$dotfilesLocalCheckoutPath" ]; then
    fancy_echo "Refreshing dotfiles from $dotfilesRepoURL ..."
    update_cloned_repo "$dotfilesLocalCheckoutPath"
  else
    fancy_echo "Cloning dotfiles ..."
    git clone "$dotfilesRepoURL" "$dotfilesLocalCheckoutPath"
  fi

  fancy_echo "Running rcm ..."
  if [ -r "$HOME/.rcrc" ]; then
    rcup
  else
    env RCRC="$dotfilesLocalCheckoutPath/rcrc" rcup
  fi
}

fetch_application_preferences() {
  fancy_echo "Setting up other app preferences ..."
  
  local prefsRepoURL="git@github.com:rcedwards/app_prefs"

  if [ -r "$prefsLocalCheckoutPath" ]; then
    fancy_echo "Updating application preferences from $prefsRepoURL ..."
    update_cloned_repo "$prefsLocalCheckoutPath"
  else
    fancy_echo "Cloning application preferences ..."
    git clone "$prefsRepoURL" "$prefsLocalCheckoutPath"
  fi
}

update_cloned_repo() {
  pushd "$1"
  git pull
  popd
}

link_sublime_text_prefs() {
  fancy_echo "Linking sublime text configuration files ..."
  local prefDestination="$HOME/Library/Application Support/Sublime Text 3/Packages/User/Preferences.sublime-settings"
  local prefSource="$prefsLocalCheckoutPath/sublime/Preferences.sublime-settings"
  ln -sF "$prefSource" "$prefDestination"
}

link_xcode_prefs() {
  fancy_echo "Linking xcode configuration files ..."
  local destThemeFolder="$HOME/Library/Developer/Xcode/UserData/FontAndColorThemes"
  local destSnippetsFolder="$HOME/Library/Developer/Xcode/UserData/CodeSnippets"
  local sourceThemeFolder="$prefsLocalCheckoutPath/xcode_config/FontAndColorThemes"
  local sourceSnippetsFolder="$prefsLocalCheckoutPath/xcode_config/CodeSnippets"
  ln -sF "$sourceThemeFolder" "$destThemeFolder"
  ln -sF "$sourceSnippetsFolder" "$destSnippetsFolder"
}

link_zsh_theme() {
  fancy_echo "Linking oh-my-zsh theme ..."
  local zshThemeDest="$HOME/.oh-my-zsh/themes/agnoster-light.zsh-theme"
  local zshThemeSource="$prefsLocalCheckoutPath/zsh_themes/agnoster-light.zsh-theme"
  ln -sF "$zshThemeSource" "$zshThemeDest"
}

link_application_preferences() {
  link_sublime_text_prefs
  link_xcode_prefs
  link_zsh_theme
}

update_dotfiles
fetch_application_preferences
link_application_preferences
```

Write your customizations such that they can be run safely more than once.
See the `bootstrap` script for examples.

Script functions such as `fancy_echo` and
`gem_install_or_update`
can be used in your `~/.bootstrap.local`.

See the [wiki](https://github.com/thoughtbot/laptop/wiki)
for more customization examples.

Debugging
---------

Your last script run will be saved to `~/bootstrap.log`.
Read through it to see if you can debug the issue yourself.
If not, copy the lines where the script failed into a
[new GitHub Issue](https://github.com/bignerdranch/cocoa-machine-bootstrap/issues/new).

License
-------

This is heavily based on Thoughtbot's [Laptop script](https://github.com/thoughtbot/laptop)

Laptop is © 2011-2016 thoughtbot, inc.
It is free software,
and may be redistributed under the terms specified in the [LICENSE] file.

[LICENSE]: THOUGHTBOT_LICENSE
