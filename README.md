Big Nerd Ranch iOS/Mac Machine Bootstrap Script
===============================================

Bootstrap script to get up and running developing iOS and Mac apps for Big Nerd Ranch

It can be run multiple times on the same machine safely.
It installs, upgrades, or skips packages
based on what is already installed on the machine.

Requirements
------------

We support:

* OS X Yosemite (10.10)
* OS X El Capitan (10.11)

Install
-------

Download, review, then execute the script:

```bash
curl --remote-name https://raw.githubusercontent.com/bignerdranch/cocoa-machine-bootstrap/master/bootstrap
less bootstrap
bash bootstrap 2>&1 | tee ~/bootstrap.log
```

Debugging
---------

Your last script run will be saved to `~/bootstrap.log`.
Read through it to see if you can debug the issue yourself.
If not, copy the lines where the script failed into a
[new GitHub Issue](https://github.com/bignerdranch/cocoa-machine-bootstrap/issues/new).

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

It should take less than 5 minutes to install (depends on your machine).

Customize in `~/.bootstrap.local`
------------------------------

Your `~/.bootstrap.local` is run at the end of the BNR Bootstrap script.
Put your customizations there.
For example:

```sh
#!/bin/sh

brew bundle --file=- <<EOF
brew "Caskroom/cask/dockertoolbox"
brew "go"
brew "ngrok"
brew "watch"
EOF

default_docker_machine() {
  docker-machine ls | grep -Fq "default"
}

if ! default_docker_machine; then
  docker-machine create --driver virtualbox default
fi

default_docker_machine_running() {
  default_docker_machine | grep -Fq "Running"
}

if ! default_docker_machine_running; then
  docker-machine start default
fi

fancy_echo "Cleaning up old Homebrew formulae ..."
brew cleanup
brew cask cleanup

if [ -r "$HOME/.rcrc" ]; then
  fancy_echo "Updating dotfiles ..."
  rcup
fi
```

Write your customizations such that they can be run safely more than once.
See the `bootstrap` script for examples.

Script functions such as `fancy_echo` and
`gem_install_or_update`
can be used in your `~/.bootstrap.local`.

See the [wiki](https://github.com/thoughtbot/laptop/wiki)
for more customization examples.

License
-------

This is heavily based on Thoughtbot's [Laptop script](https://github.com/thoughtbot/laptop)

Laptop is © 2011-2016 thoughtbot, inc.
It is free software,
and may be redistributed under the terms specified in the [LICENSE] file.

[LICENSE]: THOUGHTBOT_LICENSE
