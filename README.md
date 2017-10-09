Big Nerd Ranch macOS Bootstrap Script
===============================================

[![Build Status](https://travis-ci.com/bignerdranch/macOS-bootstrap.svg?token=mjyegwmpGK1tsHqNiqGk&branch=master)](https://travis-ci.com/bignerdranch/macOS-bootstrap)

Bootstrap script to get up and running developing iOS, tvOS, watchOS, and macOS apps for Big Nerd Ranch

It can be run multiple times on the same machine safely.
It installs, upgrades, or skips packages
based on what is already installed on the machine.

What it DOESN'T do
------------------

* It will not change your shell
	* If you're using bash or zsh we'll continue with setup, if not we'll safely exit and log what we found.
* It will not install optional utilities
	* This script aims to be the bare minimum needed for iOS development.
	* See the lean [Brewfile] for each utilitiy installed
	* To add your favorate utilities see [Local Customization])

[Brewfile]: https://github.com/bignerdranch/macOS-bootstrap/blob/master/Brewfile
[Local Customization]: https://github.com/bignerdranch/macOS-bootstrap#customize-in-bootstraplocal-and-brewfile

What it sets up
---------------

Mac OS X tools:

* [Homebrew] for managing operating system libraries.

[Homebrew]: http://brew.sh/

Unix tools:

* [Git](https://git-scm.com/) for version control
* [OpenSSL](https://www.openssl.org/) for Transport Layer Security (TLS)

Ruby toolchain (this will be helpful for project Gems such as [Fastlane](https://fastlane.tools/) and [CocoaPods](https://cocoapods.org/)):

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
* macOS Sierra (10.12) 
* masOS High Sierra (10.13)

Install
-------

Clone, review, then execute the script:

```bash
git clone git@github.com:bignerdranch/macOS-bootstrap.git
less repo_path/bootstrap
bash repo_path/bootstrap
```

Customize in `bootstrap.local` and `~/.Brewfile`
---------------------------------------------------

### Local Brewfile

A [Brewfile](https://github.com/Homebrew/homebrew-bundle) is like a Gemfile for non-ruby dependencies. Anything you would install via [homebrew](http://brew.sh/) you can drop into your `~/.Brewfile` and it will be installed during the bootstrap script.

An example Brewfile looks like this:
```ruby
# 3rd party Taps
tap 'homebrew/bundle'
tap 'caskroom/cask'

# Homebrew Packages
brew 'ack'
brew 'keybase'
brew 'tmux'

# Cask macOS Apps
cask 'java'

# Mac store Apps
mas '1Password', id: 443987910
mas 'Xcode', id: 497799835
``` 

### bootstrap.local

Your `bootstrap.local` is run at the end of the BNR Bootstrap script.
Put your customizations there.
An example local script could look like this:

```bash
#!/bin/bash

readonly prefsLocalCheckoutPath="$HOME/app_prefs"

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

link_xcode_prefs
link_zsh_theme
```

Write your customizations such that they can be run safely more than once.
See the `bootstrap` script for examples.

Script functions such as `fancy_echo` and
`gem_install_or_update`
can be used in your `~/.bootstrap.local`.

License
-------

This script is heavily based on Thoughtbot's [Laptop script](https://github.com/thoughtbot/laptop)

Thoughtbot's original work is covered under an MIT [license](THOUGHTBOT_LICENSE)

Big Nerd Ranch's modifications are also covered under an MIT [license](LICENSE.md)
