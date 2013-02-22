I wrote a [previous tip](https://coderwall.com/p/mt8ryw) which is about gitattributes. But this is not best solution to deploy Heroku. I don't want include any CSS, JSON, JS files to my git repo. But Heroku doesn't accept generate static files with any rake task. So I decide to use Redis.

[Redis](http://redis.io/) is advanced key-value store. This is quick tip for you. If you don't ready using Redis, you should try [Quick Start](http://redis.io/topics/quickstart).

So my solution is simple. Please check out my [gists](https://gist.github.com/4642734).

Here is my plan.

1. Writing SASS with previewing Sinatra

        @import bourbon/bourbon
        body
          +linear-gradient(red, green)
          color: white

2. Compile SASS and compress Rainpress then set key to Redis

        require 'rainpress'
        require 'redis'
        require 'uri'  # to parse your Redis URI

        uri = URI.parse("redis://")  # put your Redis URI like Redis to Go micro for free.
        @redis = Redis.new(:host => uri.host,
        :port => uri.port,
        :password => uri.password)
        
        task :default do
        # prepare to css
        system 'bourbon install'
        sass = `sass style.sass`
        css = Rainpress.compress sass
        
        # store to redis on heroku
        @redis.set "css", css
        p @redis.get "css" # checker
        end

    You can set CSS from local with just `rake`. Generally, we don't need set expire to our cache in this case. You can overwrite them. The cache will keep until restarting server.

3. Deploy heroku, sinatra gets CSS as String

        require 'sinatra'
        require 'redis'

        before do
          uri = URI.parse("redis://") # put your Redis URI
          @redis = Redis.new(:host => uri.host,
          :port => uri.port,
          :password => uri.password)
        end

        configure :production do
          get '/style.css' do
            @redis.get "css"
          end
        end

    Don't forget to add `$ heroku config:set RACK_ENV=production` to your app.

So I've been hitted a problem at #3. Sinatra didn't recognize a String to CSS.

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/1204/first_attempt.png)

I asked [@konstantinhaase](https://twitter.com/konstantinhaase) and he taught me (Thank you so much rkh!)

> get('/') { content_type :css; "body{color:red}" }

<small>(Oops, Coderwall can't embed his [tweet](https://twitter.com/konstantinhaase/statuses/295170545937420289).)</small>

    configure :production do
      get '/style.css' do
        content_type :css  # exactly what I want!
        @redis.get "css"
      end
    end

![Picture](https://coderwall-assets-0.s3.amazonaws.com/uploads/picture/file/1205/second_yay.png)


**Brilliant!** (but my CSS weird) Now I could serve CSS as cache. Of course this is not fully function with Sinatra. Now I learn more details about HTTP caching. Thanks for reading!

Want to see real world example? Check out my project https://github.com/nzwsch/afternoon
