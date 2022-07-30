---
title:  Rbenv shared install on Ubuntu 14.04
tags:
- rails
- deploy
---
For Ruby versions management I use rbenv.

Some of my projects are hosted on a single server, under a different user names (usually, user name equals to the name of project).

Rbenv suggests that you install it separately for every user in his home directory, which means that I have to install it everytime, when I deploy a new project, not to mention about maintainance.

It can be installed system-wide with apt-get, but I face with stale version in default ubuntu apt repository.

That's why I decide to conduct a little research how to make shared (system-wide) installation of rbenv from github.

## Preconditioning

We need `git` for work, and a number of different apps and libraries, [which used by ruby-build](https://github.com/sstephenson/ruby-build/wiki#suggested-build-environment). They all can be installed with a single command:

```bash
sudo apt-get install git autoconf bison build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm3 libgdbm-dev
```

## Installation

Let's install rbenv in a `/usr/local` directory. Why here? Because this directory 'is widely regarded as a good place in which to keep self-compiled or third-party programs' ([See same named section under this link](http://www.tldp.org/LDP/Linux-Filesystem-Hierarchy/html/usr.html)).

```bash
sudo git clone https://github.com/sstephenson/rbenv.git /usr/local/rbenv
```

Additionally, let's install ruby-build as a plugin.

```bash
sudo git clone https://github.com/sstephenson/ruby-build.git /usr/local/rbenv/plugins/ruby-build
```

## Making-ready

We need to add rbenv to a `PATH`, and also two magical lines for rbenv initialization in shell. For not to do it individually for every user, I add them to the end of `/etc/profile`.

```bash
sudo vim /etc/profile
```

In vim’s command mode (you are here right after vim starts) key `G` - goes to the end of file, `o` - inserts a new line after current, and goes into edit mode.

```bash
export RBENV_ROOT=/usr/local/rbenv
export PATH="$RBENV_ROOT/bin:$PATH"
if which rbenv > /dev/null; then eval "$(rbenv init - --no-rehash)"; fi
```

Ok, we done with that, go back to vim command mode with `ESC` key, then write changes and quit with `:wq` and `Enter` sentence.

By default, `rbenv init -` command do a shims rehash with every run, which in this case, when root rbenv directory available only in read-only mode, will make an error on shell start.
To prevent this, I add a `--no-rehash` parameter in this script.
Any way, you need to run rehash manually after each gems installation.

And I wrap rbenv initialization in condition, *just in case*.

Next, we need to create ourselves `versions` dir in rbenv root.

```bash
sudo mkdir /usr/local/rbenv/versions
```

Ok, now let's relogin for changes to take effect, or just run `source /etc/profile`. Or, even simplier `. /etc/profile`.

## Usage

Conventional use of rbenv (for example `rbenv install` or `rbenv rehash`) suppose that RBENV_ROOT directory available for user to write. But here it's not. One of the ways to solve that problem - to create a users group, which will do maintenance, with applicable permissions.

I want to go the other way - to use the `sudo` command. Сomplicacy here is that sudo reset's your environment by default for safety reasons. So, we need to run it with `-i` option.

### ruby installation

```bash
sudo -i rbenv install 2.2.0
```

Declare this version to be used by default:

```bash
sudo -i rbenv global 2.2.0
```

Tune it to not install gem docs (we don't need them in 'staging' or 'production' cases).

```bash
sudo mkdir $RBENV_ROOT/versions/2.2.0/etc
sudo vim $RBENV_ROOT/versions/2.2.0/etc/gemrc
```

Go to 'edit' mode in vim with `i` key and add this two lines:

```yaml
install: --no-document
update: --no-document
```

Save changes and close file `ESC` &rarr; `:wq` &rarr; `Enter`.

### gems installation

Now, let's install whatever gem, *for example, rails*:

```bash
sudo -i gem install rails
```

After each gems installation, directly or through 'bundle', you need to run `rbenv rehash`:

```bash
sudo -i rbenv rehash
```

***Congratulations!***

## Caveats

Commands `bundle install` and `bundle upgrade` works, and must be runned without `sudo`.

In some cases you need to preliminarily install additional libraries for some gems. For example, 'sqlite3' gem installation requires 'libsqlite3-dev':

```bash
sudo apt-get install libsqlite3-dev
```
