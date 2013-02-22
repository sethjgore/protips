Few days ago, GitHub released [Boxen]. It looks very nice, but I didn't clear how to use them. This is my guess. Please add your thoughts on comment. So let's get started.

## What is Boxen?

After finish installing, I guess it is like **Mac/GUI of dotfiles (or Homebrew)**. Every cool developers share their [dotfiles](https://github.com/search?q=dotfiles&ref=commandbar). Why people do that? I guess, it is for backup your SHELL environments or you can tell people what you use. But we can't keep our Macintosh/GUI Apps informations. (By the way, [my previous pro tip](https://coderwall.com/p/olem2w) is similar thoughts)

[Boxen] helps that. It is designed for default settings. You need few things to install your previous settings. Yeah this is great. [Boxen] allows personal or project modules too.

## Before using

**You should backup all your files to your Time Machine** because your tools will be removed. The easiest way is *clean install MacOSX*. Or remove/uninstall things which you consider to use them.

### If you don't re-install

For example,

* [Remove Homebrew](https://gist.github.com/mxcl/1173223) or `sudo rm -rf /usr/local/`
* Remove your dotfiles/vimfiles like Oh my zsh
* Remove .rbenv or .rvm directory. <br /><small>(I don't know how to migrate new location)</small>
* [Remove](http://www.freemacsoft.net/appcleaner/) your favorite App like iTerm2, TextMate (not 2), Sublime Text2...
* `chsh -s /bin/bash # this is default SHELL`

Did you feel anxious? Don't worry, you can install them with Boxen.

### If you've re-installed

After you should see "Thank you", Go to Mac App Store and download Xcode.app. And of course you need just install Command-line Tools. The requirements is all.

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/1285/Downloads.png)

### Turn on FireVault2

Boxen needs to keep your files secret I guess. Go to<br /> **System Preferences => Security and Privacy**

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/1284/privacy.png)

After restarting, **you don't need to wait finish FV encryption.** Mine is still working too ;)

[Boxen]: https://github.com/blog/1345-introducing-boxen


## Folk (or create new) repo

Boxen needs your personal repo because it is just like dotfiles. I recommend to fork them.

After forking, you should follow [GitHub's instruction](https://github.com/nzwsch/my-boxen#getting-your-users-started-after-your-fork-exists).

    # replace nzwsch to your username
    sudo mkdir -p /opt/boxen
    sudo chown nzwch:admin /opt/boxen # /opt/boxen does like your homedir
    git clone https://github.com/nzwsch/my-boxen.git /opt/boxen/repo
    cd /opt/boxen/repo # I renamed our-boxen to my-boxen
    script/boxen # this is executable

    # add boxen to your shell config, at the end. In this case I add my .profile
    echo '[ -f /opt/boxen/env.sh ] && source /opt/boxen/env.sh' > .profile

After restarting shell, So I checked new commands.

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/1286/1._bash.png)

Yeah, now our new homebrew was installed at `/opt/boxen/homebrew/bin/brew`. But wait, `git --version` is a little bit old. You can use native like `brew upgrade`

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/1287/1._bash2.png)

See? There are no problem to upgrading. Of course you can manage Formulas like Bundler. I believe now you can understand exactly what I am talking about. (Sorry for bad conversations!)

## Add what you want

This is not enough because our favorite editor is still gone. We want bring back them. But no more open Safari. You can do with Boxen. This is one of the greatest feature of it.

First, check out [a bunch of list of softwares](https://github.com/boxen). There are lot of the great Mac apps. In this case, I'll show you how to add [TextMate](http://macromates.com) not 2.

<small>*Note:* we need to send a pull request TextMate2 to [https://github.com/boxen](https://github.com/boxen).</small>

### Puppetfile (Specify version)

Next, open up `/opt/boxen/repo/Puppetfile` and add *last* 1 line.

    # Core modules for a basic development environment. You can replace
    # some/most of these if you want, but it's not recommended.

    github "dnsmasq",  "1.0.0"
    github "gcc",      "1.0.0"
    github "git",      "1.0.0"
    github "homebrew", "1.0.0"
    github "hub",      "1.0.0"
    github "inifile",  "0.9.0", :repo => "cprice-puppet/puppetlabs-inifile"
    github "nginx",    "1.0.0"
    github "nodejs",   "1.0.0"
    github "nvm",      "1.0.0"
    github "ruby",     "1.0.0"
    github "stdlib",   "3.0.0", :repo => "puppetlabs/puppetlabs-stdlib"
    github "sudo",     "1.0.0"
    # add following rule below
    github "textmate", "1.0.0"

It has rule that `github "name", "version"`. We need find `name` and `version`.

- `name`:  [puppet-textmate](https://github.com/boxen/puppet-textmate) by `"boxen/puppet-#{name}"`
- `version`:  from [boxen/puppet-textmate/tags](https://github.com/boxen/puppet-textmate/tags)

This looks just like [Homebrew formula](https://github.com/mxcl/homebrew/wiki/Formula-Cookbook).

### site.pp (Adding puppet module)

Then, open `/opt/boxen/repo/manifests/site.pp` and add *last* 1 line.

    node default {
      # core modules, needed for most things
      include dnsmasq
      include git
      include hub
      include nginx
      include nvm
      include ruby
      # add following rule below
      include textmate

This point is easy. You can find at [README](https://github.com/boxen/puppet-textmate#usage)

After editing, You need to run `boxen`. It is same thing to `script/boxen`

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/1288/1._bash_3.png)
![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/1289/Untitled.png)

*Tada*, we did install by Terminal! It is super! I want to appreciate for the Boxen project. :D

After install, don't forget push your repo on GitHub.

## That's it!

Thanks for the reading. There is a few informations to know about. I hope to more tuts on the internet.
