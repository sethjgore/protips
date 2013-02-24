Hi there! This is not *"pro"* tip perhaps this may help you. It seems many GitHub users doesn't set their pages. Did you know *GitHub Pages*? This is your own space put any contents what you want. It is great idea to put yourself if you don't have your blog/company website. 

## The Setup

Did you know [The Setup](http://usesthis.com/)? It is really cool project. You can answer 4 questions.

- Who are you, and what do you do?
- What hardware are you using?
- And what software?
- What would be your dream setup?

There are very famous persons interviews like [Tom Preston-Werner](http://tom.preston-werner.usesthis.com/), [Chris Wanstrath](http://chris.wanstrath.usesthis.com/), [_why](http://why.usesthis.com/) or many many talented people. But I believe The Setup is not only for professionals. It allows Share Alike in [CC-SA 2.5](http://creativecommons.org/licenses/by-sa/2.5/). **I know you are a great programmer, you should answer to tell us your interesting stuff ;)**

## Yeah, this is good idea. But how can I do this?

You can begin with Original repo or my forked project.

### Your experiences

If you want start with on your way, I recommend pull [jekyll branch](https://github.com/waferbaby/usesthis/tree/jekyll). But this repo is so large. You may confuse its directory. Here is a list you won't need.

    ./hardware/ # It does not work
    ./software/ 
    ./_layouts/.DS_Store
    ./_plugins/  # GitHub page does not work _plugins
    ./_posts/*.interview # change file extension to markdown

Of course you can use any static site generator like [nanoc](http://nanoc.ws), [Wintersmith](http://jnordberg.github.com/wintersmith/). You should create another branch and keep your source like [Chris Eppstein does](https://github.com/chriseppstein/chriseppstein.github.com/tree/source).

### Fork my repo

Also you can [fork my repo](https://github.com/nzwsch/nzwsch.github.com/tree/d6414fb4ab0838ecf335967ca0eeb5d57476b12f) and rename to your `username.github.com`. My fork was based on original design (I like original design). You can put your post in this rule.

* your portrait filename is same with post filename.
* your post requires *layout is post*, *person is your name* and *summary is your description*
* Answer 4 basic questions with markdown (with images, lists, quotes, links..)
* **Add/Update your interview every year.**

## What is my additional idea?

You have noticed the permalink is */interviews/:year*. The reason is here. You are changing from now to future. Of course your setup and thoughts are changing. Put your thoughts for every year. Maybe you change or not but almost changes. I added an archive page. You will be able to discover my thoughts (Yeah I'm just a wannabe).

## How to customize or change a color?

If you know about SASS and Compass, you can glob my code from [my gist](https://gist.github.com/nzwsch/4721404) and you can set your favorite color to `$key`. [HSL Color Picker](http://hslpicker.com/) will help you.

    $ curl https://gist.github.com/nzwsch/4721404/raw/screen.sass > _sass/screen.sass
    $ compass compile --sass-dir _sass --css-dir stylesheets # not _site

And you can change fonts from [Google Web Fonts](http://www.google.com/webfonts).

## Bonus?

If you do this, you can earn **open source badge** like [me](https://coderwall.com/nzwsch/achievements/179515).

That's it. I hope you consider to put your interview. Enjoy :)
