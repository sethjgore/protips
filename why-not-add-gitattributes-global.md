---
ple who loves git, they know that add [global ignore file] to ignore files at  every project. In my case, I love write SASS and it makes cache files. So that I run `git config --global core.excludesfile ~/.gitignore_global`.

[global ignore file]: https://help.github.com/articles/ignoring-files

I always compile SASS to CSS file and I want to do minify them. Ignoring Cache and CSS file is good idea. But when you have to keep CSS file (like Jekyll, nanoc, Octopress...) at production, you are patient tracking CSS in git. Like this.

    $ git log -p public/css/style.css  # on my repo

    diff --git a/public/css/style.css b/public/css/style.css
    index 13b75fa..0fbe44d 100644
    --- a/public/css/style.css
    +++ b/public/css/style.css
    @@ -1 +1 @@
    -html,body,div,span,applet,object,iframe,h1,h2,h3,h4,h5,h6,p,blockquote,pre,a,abbr,acronym,address,big,cite,code,del,dfn,em,img,ins,kbd,q,s,samp,small,strike,strong,sub,sup,tt,var,b,u,i,center,dl,dt,dd,ol,ul,li,fieldset,form,label,legend,
    \ No newline at end of file
    +html,body,div,span,applet,object,iframe,h1,h2,h3,h4,h5,h6,p,blockquote,pre,a,abbr,acronym,address,big,cite,code,del,dfn,em,img,ins,kbd,q,s,samp,small,strike,strong,sub,sup,tt,var,b,u,i,center,dl,dt,dd,ol,ul,li,fieldset,form,label,legend,
    \ No newline at end of file

That looks too dumb. Now we need Git makes smarter.

> One cool trick for which you can use Git attributes is telling Git which files are binary (in cases it otherwise may not be able to figure out) and giving Git special instructions about how to handle those files. For instance, some text files may be machine generated and not diffable, whereas some binary files can be diffed — you’ll see how to tell Git which is which. ([Git Book](http://git-scm.com/book/en/Customizing-Git-Git-Attributes))

I believe every compiled files should be binary. I thought my .gitattributes is here.

    $ cat [my_repo]/.gitattributes
    
    *.css  binary
    *.js   binary
    *.json binary
    
    # and probably every blog post should binary?
    *.md binary

After add this file, check your repo again.

    $ git log -p public/css/style.css
    
    diff --git a/public/css/style.css b/public/css/style.css
    index 13b75fa..0fbe44d 100644
    Binary files a/public/css/style.css and b/public/css/style.css differ

Neat. `.gitattributes` seems hide/cover logs. The txt-diff has been kept. If you add `.gitattributes` git files doesn't change. But we need one more thing.

    $ git config --global core.attributesfile ~/.gitattributes_global

This command made my day. So don't you try this?
