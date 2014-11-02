---
layout: post
title: Github Pages to Heroku
---

# Synopsis

My initial goal was to put my jekyll website on Heroku but I have encountered many problems due of my bad Ruby level and ``pygment`` usage.

During month my jekyll website was freely hosted by __GitHub Pages__. Thanks for all the fish. But __GitHub__ is not for app hosting. 

Many hours of:

> "What the fuck I have 404 ?"  
> "Oh Damn it my local Jekyll render it correctly and not on Github..."  
> "Ohh enough, it was just the yaml header with quotes which give me fucking 404"

# Idea

I think it's important to use local rendering to be sur of it and don't have surprise after deployment.

* ``www.socketubs.net`` is a github repo (not Github Page)
* ``www.socketubs.net/_site`` is a Heroku repo (Html app ([see][1]))
* ``www.socketubs.net/make.sh`` is a little bash script which do all the job

Jekyll is rendered locally, hosted on Heroku, backuped on Heroku and Github for versionning.

By the way you can see a ``404.html`` page.

{% highlight bash %}
#!/bin/bash

#############################################################
# Variables
heroku_repo="_site"
heroku_url="git@heroku.com:socketubs.git"
heroku_commit="New release"
github_commit="New release"
#############################################################

function usage () {
	if [ $# -lt 1 ]; then
		echo "Usage: ./make (run|deploy|open)"
		echo ""
		echo " run         Run jekyll into _site"
		echo " deploy      Deploy your website on heroku and save it on github"
		echo " open        Open local and remote version in browser"
		exit 1
	fi
}

function make_heroku () {
	cd _site
	if ! [ -e ".git" ]; then
		git init
		git remote add heroku $heroku_url
	fi
	touch index.php
	echo "php_flag engine off" > .htaccess
	echo "ErrorDocument 404 /404.html" >> .htaccess$
	cd ..
}

function update_heroku () {
	cd _site
	git add .
	git commit -a -m "$heroku_commit"
	git push -u -f heroku master
	cd ..
}

function update_github () {
	git add .
	git commit -a -m "$github_commit"
	git push origin master
}

if [ "$1" == "run" ]; then
	jekyll
	make_heroku
	exit 0
elif [ "$1" == "deploy" ]; then
	make_heroku
	update_heroku
	update_github
	exit 0
elif [ "$1" == "open" ]; then
	cd _site
	heroku open
	open http://localhost:4000
	python -m SimpleHTTPServer 4000
	cd ..
else
	usage
fi
{% endhighlight %}

# Finally

And finally we have this workflow:

* Write your articles
* ./make run
* ./make open
* See local version and online version
* Correct if you want
* ./make deploy

Your Github repo will be updated and heroku will received new website version.

[1]: http://kennethreitz.com/static-sites-on-heroku-cedar.html