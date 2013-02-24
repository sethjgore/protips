How do you commit when you use git? Most developers like starting with small-case like this.

    $ git commit -m "blah blah blah" #=> "Blah blah blah"

Because we are lazy to change "Add", "Remove", "Change"... But when you merge another branch, git will add commit message which starts upper-case  "Merge ..."

So I decided to wrote a simple scripts. [https://gist.github.com/4472134](https://gist.github.com/4472134)

    #!/usr/bin/env ruby
    msg = File.read(ARGV[0]) # ARGV[0] == .git/COMMIT_EDITMSG
    File.open(ARGV[0], 'w') do |f|
      f.write(msg.capitalize)
    end

This script helps us change capitalisation when you commit things. Usage is simple. Put code into your every `.git` directory. (I recommend you to change code with C, Java... You can get bit faster to run the command)

*Note:* It might ignore `...CSS...` to `...css...` by capitalise method. I wanted to keep script is simple. Please edit what you want.

    $ vim commit-msg       # edit above
    $ chmod a+x commit-msg # executable command
    $ mv commit-msg .git/hooks

It's done, but wait. How do we change already committed messages? You can use `filter-branch`. If you need more instructions, go check out [Git Book].

    $ cd [your git repo]
    $ git filter-branch -f --msg-filter 'ruby -e "puts ARGF.read.capitalize"' -- --all

Okay, did you see every commit capitalised? That's it! But don't forget you should not do this on already pushed to shared server. Hope this helps ;)

[Git Book]: http://git-scm.com/book/en/Git-Tools-Rewriting-History

[]($ git filter-branch -f --msg-filter 'sed "s/^./\u&/g"' -- --all)
