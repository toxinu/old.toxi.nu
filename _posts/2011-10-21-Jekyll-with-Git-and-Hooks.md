--- 
layout: post
title: Jekyll with Git and Hooks
---

Good bye my lovely [Pyame][1]... Hello [Jekyll][2] ! Pyame is my personnal static website creator written in Python under heavy development. These days I searched powerfull, light and easy to use static website creator, and Jekyll is arrived. Thanks God.

In short, these programs parse differents files written in a special syntax, for example : Markdown, Textile, etc. An easy syntax to make your articles or docs more swag. And it generate html output.

I love the usage system of Jekyll, Hyde, Pyame, Bonsai... [more here][3]. But the main incovenient thing is that you can't write articles or docs through your website browser. And you need one of these generator installed on each computer you can potentialy write text.

But ! But ! Jekyll + Git + GitHooks are __awesome__ !

Put your jekyll website into a git repo on your server, git clone it on your desktop, write articles, add, commit, push and githook will automaticaly execute jekyll rendering.

__Desktop__ :

 * git-core

__Server__  :

 * git-core
 * jekyll
 * nginx (optionnal)

## Jekyll

Jekyll installation is easy like trolling about proprietary software. [Docs][4].

{% highlight bash %}
sudo gem install jekyll
{% endhighlight %}

Next ? I think you know how to install git instance. There are too many tutorials and docs on the Internet.

## Githooks

Githooks are specials scripts which will be execute when git received, a push, a commit, an update...
Look at your _.git/hooks_ folder on your server:
{% highlight bash %}
socketubs@socket-desktop:~/Repositories/canopsis$ ls -lh .git/hooks/
total 44K
-rwxrwxr-x 1 socketubs socketubs  452 2011-10-21 21:04 applypatch-msg.sample
-rwxrwxr-x 1 socketubs socketubs  896 2011-10-21 21:04 commit-msg.sample
-rwxrwxr-x 1 socketubs socketubs  160 2011-10-21 21:04 post-commit.sample
-rwxrwxr-x 1 socketubs socketubs  552 2011-10-21 21:04 post-receive.sample
-rwxrwxr-x 1 socketubs socketubs  189 2011-10-21 21:04 post-update.sample
...
{% endhighlight %}

A short description is present at the top of each files.

This is my __post-receive__ file :
{% highlight bash %}
root@socketubs:/srv/gitosis/repositories/socket_site.git/hooks# cat post-receive
#!/bin/sh

echo "-- Attempting to pull and launch jekyll --"
cd /var/www/socket_site
git --git-dir /var/www/socket_site/.git pull file:///srv/.../socket_site.git/ master
jekyll
echo
{% endhighlight %}

## The pie on the cherry

Just write your files in a beautiful simple syntax with the best text editor ever, __VIM__, commit and push. 
Like a Boss.

LOVELY ?

[1]: http://socketubs.github.com/TheMall
[2]: http://jekyllrb.com/
[3]: http://iwantmyname.com/blog/2011/02/list-static-website-generators.html
[4]: https://github.com/mojombo/jekyll/wiki
