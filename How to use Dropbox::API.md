**Note:** I missed this tuts won't help with using Rails...

I've considered to my new app needs Dropbox as storage. So I want to use dropbox with ruby. But how? I decided use [Dropbox::API](https://github.com/futuresimple/dropbox-api) not official SDK. This tutorial is very basic. I'll show you how to connect with this api. It's really easy.

## Still not resist at dropbox as developer?

[Go check out this page first](https://www.dropbox.com/developers/apps). Next, you `Create an app`. In this case, I choose App Type is Core API and Access to App folder. Then you can get a pair of *App key* and *App secret* at page like below. It is required to next chapter.

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/1163/Screenshot_1_21_13_11_39_AM.png)

## Get ready for your app. (Skip if you are not new to Ruby)

Next, you should install gems. For example, I preferred `Gemfile` and `Rakefile` with my small app called [setup](https://github.com/nzwsch/setup). And you need to files like below.

*Gemfile*

    source :rubygems
    gem 'dropbox-api'

*Rakefile*

    require "dropbox-api/tasks"
    Dropbox::API::Tasks.install # this is required for link your apps.

Files are look good. Now you run `bundle install`, and `gem list | grep dropbox-api`. You should get `dropbox-api (0.3.2)` on your console.

## Get tokens from Rake (Link our app to Dropbox)

Okay. Next you try this.

     $ rake -T
    rake dropbox:authorize  # Authorize wizard for Dropbox API
    rake spec               # Run RSpec code examples

Did you see `dropbox:authorize`? It helps to get our tokens. Let's try this.

    nzwsch@astraea ~/c/afternoon> rake dropbox:authorize
    Enter consumer key: [your_key]
    Enter consumer secret: [your_secret]
    
    Go to this url and click 'Authorize' to get the token:
    https://www.dropbox.com/1/oauth/authorize?oauth_token=vkqdd1ctfsccc1d
    
    Once you authorize the app on Dropbox, press enter…

Did you get your console like this? Of course you go to your *temporary* URL and click authorise.

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/1164/Screenshot_1_21_13_12_13_PM.png)

 Did you get your token and secret correctly? **You should keep these information carefully.** If you have no problem, go to next chapter.

## Check work correctly with testing

If you don't know about TDD, I recommend you to read [RSpec Book](http://pragprog.com/book/achbd/the-rspec-book). Reading specs are very good way to understand how they works. This is my sample imitated from [original](https://github.com/futuresimple/dropbox-api/blob/master/spec/spec_helper.rb).

*spec/spec_helpers*

    $root = File.expand_path('../../', __FILE__)
    require 'yaml'
    $config = YAML.load_file(File.join($root, 'config/env.yaml'))

*spec/your_spec.rb*

    describe Your_App do
      context "Config" do
        it "loads env.yaml correctly" do
          $config["DROPBOX_APP_KEY"].should_not be_nil
          $config["DROPBOX_APP_SECRET"].should_not be_nil
          $config["DROPBOX_APP_MODE"].should_not be_nil
          $config["DROPBOX_SECRET"].should_not be_nil
          $config["DROPBOX_TOKEN"].should_not be_nil
        end
      end
    end

*config/env.yaml* # change filename what you want

    DROPBOX_APP_KEY: [your_key_from_web]
    DROPBOX_APP_SECRET: [your_secret]
    DROPBOX_APP_MODE: sandbox
    DROPBOX_TOKEN: [your_token_which_shown_on_console]
    DROPBOX_SECRET: [your_secret]

Next, you run `rake spec` and I skip to start from red in this tutorial. You should see 1 example passed. And you should not track env.yaml VCS. It's containing secrets. Tired to read? Goal is close. Let's link to dropbox.

## Add few lines to your_spec.rb

*config/env.yaml*

    describe Dropbox::API do
      let(:client) do
        Dropbox::API::Config.app_key = $config["DROPBOX_APP_KEY"]
        Dropbox::API::Config.app_secret = $config["DROPBOX_APP_SECRET"]        Dropbox::API::Client.new(
            :secret => $config["DROPBOX_SECRET"],
            :token  => $config["DROPBOX_TOKEN"]
        )   
      end 

      it "gets successfully work with dropbox api" do
        client.account.should be_an_instance_of Dropbox::API::Object
      end 

      # Edit what you want
      it "list directory but returns empty array" do
        files = client.ls
        files.should == []
      end 
    end 

API provides us to easy way to access files with few codes. The important thing is

    Dropbox::API::Config.app_key = $config["DROPBOX_APP_KEY"]
    Dropbox::API::Config.app_secret = $config["DROPBOX_APP_SECRET"] 

This codes prepare initial setup. Dropbox SDK seems need every object begins like `DropboxSession.new(APP_TOKEN, APP_SECRET)`. But Dropbox::API is only need two lines. I could not tell you exact things. Please check out original [README](https://github.com/futuresimple/dropbox-api#what-differs-this-from-the-dropbox-ruby-sdk)

Uh, please try `rake` first. Did you see 3 example are passed? If you so, congrats to see them. If not please check your `config/env.yaml` and check your tokens are collect. Now you can try with `client` and please check out [futuresimple/dropbox-api#dropboxapiclient-methods](https://github.com/futuresimple/dropbox-api#dropboxapiclient-methods)

Yep. This is it. Did you enjoy the post? If you have a question, please ask me on [Twitter](https://twitter.com/nzwsch/).

### Bonus

New to Dropbox user? [Please invite me to get your extra space](http://db.tt/ecOiBpZu). Thanks. :)
