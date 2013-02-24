Thanks for up vote for my previous post. I'll show you more detail of usage and the sneak a peek my project. Let me introduce my project first. I write my blog post and deploy at heroku. And I want to write markdown with great UI of [Day One.app](http://dayoneapp.com). Day One can drop a post to Dropbox directly. So I decided to use them. This is quick dirty code but you can start blogging.

Did you ready to use Dropbox::API? If you not, please read previous post first.


## Setup Your DayOne Journal

I don't want to install Dropbox.app so in this case I use iOS version. Please turn off iCloud sync, and turn on Dropbox, and check your Dropbox folder. I recommend to try with no posts.

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/1174/IMG_0004.png)

*Note:*  Backup your previous posts, they are stored at `~/Library/Mobile Documents/***~com~dayoneapp~dayone/Documents/Journal_dayone/entries`

## Change Dropbox settings

Did you notice all posts are uploaded at `(Your_Dropbox_Dir)/Apps/Day One/Journal.dayone/entries/*.doentry`? If you read previous my post, you can't access to DayOne journal. We need change settings first. Go to [My Apps](https://www.dropbox.com/developers/apps) and remove previous settings (we can't toggle access type). And create new one and choose Access is **Full Dropbox**. Yes, now you can access whole Dropbox directory.

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/1177/Screenshot_1_23_13_4_27_PM.png)

*Note:* Don't forget to run `rake dropbox:authorize`for new app.

And you need change your code. If you have *config/env.yaml*

 `DROPBOX_APP_MODE: dropbox`

Or directly,

`Dropbox::API::Config.mode = 'dropbox'`

`sandbox` is only within your App folder, `dropbox` is whole Dropbox directory.

## Try it out

I prepared [dirty code from gists](https://gist.github.com/4617136). Please download this on any directory, run `rspec --color dropbox-api_spec.rb`

First spec must fail because your account name didn't set. Change your name and run rspec again, so your specs are passed.

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/1178/Screen_Shot_2013-01-23_at_5.06.54_PM.png)

The important thing is all data will be receive `Hash`. Did you try `Dropbox::API::Client#download`? API is not GUI App. You need receive data and write them. I'll show you how after.

Want to know more `client.account` data? Comment out line 23, you can get all account data.

    #<Dropbox::API::Object country="JP" display_name="Seiichi Yonezawa" email="me@nzwsch.biz" quota_info=#<Dropbox::API::Object normal=*** quota=*** shared=0> referral_link="https://www.dropbox.com/referrals/***" uid=***>

This is very cool RSpec tip. You don't need to put `p` onto your code  anymore!

## Where is my day one entries?

This is very basic tutorial so I didn't use Sinatra in this lesson. But in this pro tip, we create simple file uploader/downloader with API.

API does not have way to access your directory like `Dir.chdir`. Please comment line 28 and run spec.

Did you pass the spec? (or not?) If you have a one or more posts, you will  pass. Did you notice `clients.ls` returns an Array? It is like a simple database. If you didn't pass 2nd spec, add any file.

Now you can add real code. Please add this code to your *dropbox-api_spec.rb*

    class MyApp do
      def initialize
        @client = Dropbox::API::Client.new(:token  => $config["DROPBOX_TOKEN"],:secret => $config["DROPBOX_SECRET"])
        @posts  = @client.ls 
      end 
    
      def lists_all_posts
        @posts.length
      end 
    end

And comment out 3rd pending, run your spec.

Did you pass your spec? What's happen? Don't worry this is no problem. I'm just kidding. Your code at `@posts = @client.ls` does look up your top of directory. Now you should change like `@posts = @client.ls "Apps/Day One/Journal.dayone/entries"` So you can pass all your specs?

Did you notice what's difference 2nd and 3rd specs? 2nd one means directly run `Dropbox::API::Client#ls` but 3rd one is real ruby code and it must same value with 2nd spec. This is writing Test with RSpec I guess. (But I don't know more details about RSpec.)

Yep this is the end of basics. Let's work more codes.

## Get your day one journal post text and date

We can access all posts. But how can we get specify post? It's little harder. Because DayOne journal file is `.plist` as XML. It contains markdown post, date format, which has star or not… We need another Gem to solve this problem.

`$ gem install plist`

Thanks for the great library [Plist Gem](http://plist.rubyforge.org). It is very simple way to parse/generate plist file.

Add your *dropbox-api_spec.rb* to `require 'plist'`.  I believe RSpec can check to work your new gem. Please create a new file and save to new `example.plist`


    <?xml version="1.0" encoding="UTF-8"?>
      <!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
      <plist version="1.0">
      <array>
          <integer>1</integer>
          <string>two</string>
          <dict>
            <key>c</key>
            <integer>13</integer>
          </dict>
      </array>
      </plist>

Did you check 4th spec was passed? Now we try to read from day one journal.

We use [Dropbox::API::Client#download](https://github.com/futuresimple/dropbox-api#dropboxapiclientdownload) this time. This method bring data as not file, so you don't worry to duplicated files when you run spec. Now let's try. Please comment out 5th spec.

Every files on the dropbox has data like,

    client_mtime="Tue, 22 Jan 2013 05:35:55 +0000" # date when file updated
    modified="Tue, 22 Jan 2013 05:35:55 +0000" # date when file modified
    path="/Apps/Day One/Journal.dayone/entries/00CE9B175F204D52998085D6C7BA7D5E.doentry" # path

You can get more info [REST API reference](https://www.dropbox.com/developers/reference/api#files-POST).

My first fault was `client_mtime` is not same `file created_time`. And we can't sort every posts by this value. The only important thing is `"path"`. This data is just `Hash`. We use this value and get *just top of doentry from the list*.

Did you pass 5th spec? I know you couldn't. This is my second misunderstood. But we can get `.doentry` path. Why?

The reason why Dropbox::API::Client#download doesn't know "/" (root) symbol. So we need remove it to pass the spec. Change your spec to

    client.download(post["Path"].sub("/", ''))

Did you know this mean? It's simple. Only Path begins with "/", `sub` remove first "/" only. If you use `gsub("/", "")` this removes all "/" symbols from String.

Did you pass this time? If you use non-ASCII characters like `Café`? , (I use Japanese usually) You can't get correct data. Day One use UTF-8. So we can solve put `force_encoding("utf-8")` with this problem.

Let's grow real ruby code up.

    def get_first_post
      post = @client.download(@posts.first["path"].sub("/", ""))
      Plist::parse_xml(post.force_encoding("utf-8"))
    end

Yeah, MyApp#get_first_post is just `Hash`. Now you can comment out 6th spec. You can get your content with only Ruby and Dropbox::API. Here's quick tip for you.

    require 'redcarpet'

    post = MyApp.new.get_first_post
    markdown = Redcarpet::Markdown.new(Redcarpet::Render::HTML)
    markdown.render(post["Entry Text"])

That's the all folks! Thanks for reading stuff.

## Bonus

Want to know more? I'll show you one more thing.

If you don't have DayOne.app, you couldn't enjoy this post. But here is the way to create new one. I need to convert my blog posts as DayOne. I will describe `Dropbox::API::Client#upload`

This is my [gists](https://gist.github.com/4592411). You need to install [DayOne CLI Tools](http://dayoneapp.com/tools/).

My blog post files are simple. I manage all posts from Yajl-JSON and database file. If you use Jekyll/Octopress(I love their projects but I don't think this is good way to deploy Heroku), You should read [Cameron Diagle's Blog](http://camerondaigle.com/articles/from_expressionengine_to_sinatra_and_heroku).

You need to tell ruby to an array which contains all posts as hash.

    # my hash looks like this
    # [{"datetime":"2012-12-19 14:03:47 +0900","filename":"posts/cb4fe97da53534f69a850f83a0e1150f6acd7034.md","permalink":"blogging-with-sinatra","tags":["ruby","sinatra"],"title":"Blogging with sinatra"}]
    hash = Yajl::Parser.new.parse(File.read('database.json'))
    
    hash.each do |h|
      if FileTest.exist?(h['filename'].sub(/^posts\//, ''))
        system "dayone -d='#{h['datetime']}' new < #{h['filename'].sub(/^posts\//, '')}"
      end
    end

It means `$ dayone -d=2012-12-19 new < "blog contents"` as loop. So ready to migration. We can put these generated files puts directly via browser, Dropbox.app. But I tried to put with API.

In conclusion, this doesn't mean nothing so I comment them out. But this code is still good for practice.

    dayone = Dir.glob('*.doentry').sort_by! do |p|
      foo = Plist::parse_xml(File.read(p))
      foo["Creation Date"]
    end

This is local. We can directly parse plist. and sort by real created date. `dayone` has simple array.

    dayone.each do |d|
      client.upload(
          'Apps/Day One/Journal.dayone/entries/' + File.basename(d),
          File.read(d)
      )
      p "#{d} has completed."
      sleep 1
    end

`Dropbox::API::Client#upload` needs two values. First one is the Path which you want to save. Another one is content. It tools long time. I put status message every post finished uploading.

But dropbox's mtime didn't sorted what I want.

## Try them.

I couldn't show you all of methods, but these are very simple and easy to use. But every download needs few seconds. You should contrive what you use your plan.

If you want to list all posts like sinatra, I recommend to make database file with YAML, JSON, or XML whatever. You should stored its path.
