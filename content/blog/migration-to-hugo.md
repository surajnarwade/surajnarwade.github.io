+++
date = "2017-03-13T21:21:49+05:30"
title = "Migration to Hugo"
category = "Blog"
author = "Suraj Narwade"

+++


Hello guys, its been so long, I havn't written any post.
Recently, I have migrated my blog from [Pelican](https://blog.getpelican.com/)(python based static site generator) to [Hugo](https://gohugo.io/)(GO based static site generator).
Today, I will write about it. You can explore my blog at [surajnarwade.github.io](https://surajnarwade.github.io)

*Installing Hugo*
-----------------
.

* Install hugo 

```
$ go get -v github.com/spf13/hugo
```

* Check whether it is properly installed or not

```
$ hugo help
```

*Using Hugo*
------------
.

* We need to tell hugo to generate blog skeleton, type following command for that:

```
$ hugo new site myblog
```

* `myblog` directory will be created, checkout into directory:

```
$ cd myblog
$ tree 
.
├── archetypes
├── config.toml
├── content
├── data
├── layouts
├── static
└── themes

```

* Now its time to write to post. following command will create a post with predefined skeleton. you can create your own archtypes for this.
Edit the post with markdown syntax. Click [here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) for markdown cheatsheet.

```
$ hugo new post/good-to-great.md
```

* Compile the blog using following command and our website will be generated under `public/` directory.

```
$ hugo
```

* Hugo comes with server while reloads your browser live as soon as there's change in a file so, you will get a instant feedback.

```
$ hugo server
...
....
Watching for changes in /home/snarwade/myblog/{data,content,layouts,static,themes}
Serving pages from memory
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop

```
* Now Open [localhost:1313](localhost:1313) on your browser to see the website. you can make changes to blogpost file to see live changes.

* I have used [nix](https://github.com/LordMathis/hugo-theme-nix) theme for my blog, but there are bunch of beautiful themes for hugo from which you can choose, Click [here](http://themes.gohugo.io/) to get browse hugo themes.

```
$ cd themes
$ git clone https://github.com/LordMathis/hugo-theme-nix
```

* Update `config.toml` file

```
languageCode = "en-us"
title = "<your title>"
baseURL = "https://<username>.github.io/"
theme = "your theme name"
disqusShortname = "your disqus shortname"

[params]
  GithubID = "your_github"
  GitlabId = "your_gitlab"
  TwitterID = "your_twitter"
  LinkedInID = "your_linkedin"
  GoogleplusID = "your_googleplus"
  FacebookID = "your_facebook"
  InstagramID = "your instagram"
  Name = "your_name"
  HeaderUsername = "username"
  HeaderHostname = "hostname"
  Email = "your_email"
  About = "info_about_you"
  ProfilePicture = "profile_picture_url"
  GoogleAnalytics = "your_google_analytics_id"
```

*Deploying blog to Github*
------------------------


* Create repository named as <username>.github.io, this will be the URL for your blog. Here, Master branch will serve contents for the blog. 

```
$ git clone git@github.com:<username>/<username>.github.io.git
```
* Copy the contents from public directory to repository and push the contents to github
```
$ cp -r public/* <username>.github.io/
$ cd <username>.github.io
$ git add --all
$ git commit -m 'First commit'
$ git push
```

* You will have your own new blog at `<username>.github.io`

`Happy hacking || Happy writing !!!`

(Comments are most welcome)
