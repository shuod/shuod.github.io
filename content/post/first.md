+++
date = "2016-04-02T10:40:00-06:00"
title = "How to setup a static blog site using Hugo (hosted on github)"
tags = ["VPS", "Linux","VPN"]
Categories = [ "Linux"]
+++

Huog is a blog generating system written in go. You could give the article in Markdown format and it will then render the article into html files. Github.com is the place where it uses git to do the version control and they now provide each account a static blog system. So combine the two together, we then have an ideal personal blog with version control. This will include the following topic:

* Generate a blog site using hugo
* Host the webpage source code on github.com
* Host the hugo output(webpage to show) on github.com

<!--more-->

## Generate a blog site using hugo

Hugo is a static webpage generator written in Golang. Unlike other similiar software like Jekyll, it features a faster speed. In a tutorial on Hugo (https://www.udemy.com/build-static-sites-in-seconds-with-hugo/), the author used Hugo to compile 5000 webpages and it finished in 6 seconds.

### Download and install Hugo

Before we get started, you need to install Hugo. And the compiled binary code for different OS platform could be found here: https://github.com/spf13/hugo/releases

### Generate the website folder

First, run hugo to generate several template folders in the place where you want to hold the blog (blogsite\) :
`hugo new site blogsite`
This will generate standard template folders under the new folder "blogsite".

### Generate the first webpage

Second, we could now generate individual blog pages. Suppose we now want to write  the first blog (blog_1.md), we should run:
`hugo new blog_1.md`

where the "*.md" extension name indicates this file is in "Markdown" language. Markdown is similiar to Tex or Html language, since they all use different "label" to tell the computer how to render the text followed and they are all ASCII file. The difference is "Markdown" uses very simple and human friendly grammar, which makes it very powerful when you need to focus on writing instead of coding "label"s.

If you open the file with any text editor, you should see something similiar to
```
+++
date = "2016-01-12T10:33:28-06:00"
draft = true
title = "blog_1"
Catogories = "Coding tutorial", "Golang"
Tags = "Golang", "Github", "Blog"

+++
```

The "draft = true" tells hugo that this page is only a draft and by default this page will not be complied to show in your website. In order to let this page be displayed, you have two choice: either to change this to "draft = false" or use a special switch when running the HTTP server from hugo. Here we choose the first way. we change to "draft = false".

Then we write something in the file to give hugo something to show off:

` # This is my first line!`

"#" is part of the writing too.

### Compile the webpage

Before we compiling the webpage, we still need to download a theme for the blog site. This theme will decide how to display and where to display your blog. All of the theme are available here: http://themes.gohugo.io/


My first theme trial is "Cactus" because I just had them! 

If you don't have a github account, you can download the zip file from: https://github.com/digitalcraftsman/hugo-cactus-theme. Then create a new folder call "themes" just under "blogsite". And also create a new subfolder called "cactus" just under "themes". You can extract every subfolders and files into folder "cactus".

So we have the source code of the webpage, which is in Markdown language. We will now compile it before it could be published. To do so, go to "blogsite" folder and run (-t meaning to use the theme under that folder):
`hugo -t cactus`
Then it will show some running reports, like:

```
0 draft content
0 future content
1 pages created
...
in 49ms
```
if you still leave "draft = true" then you will have 1 draft content insead of 1 pages created. You will see a new folder generated in the blogsite called "public\", which has all of the html files needed to display.

### Display the webpage and using a browser to check

In the "blogsite" folder, run:
`hugo server -t cactus`
then the compiled webpage will be powered by the HTTP server built in hugo and then you could use your browser and check the IP:
`http://127.0.0.1:1313`

You should have a clean and beautiful webpage showed up!

If you want to try another theme, just download another one and extract it under a different subfolder (suppose it is bow) then run:
`hugo -t bow`
and use following to show:
`hugo server -t bow`

This is just for local monitoring. If you are going to publish this online, you still want to change the file "config.coml":
```
baseurl="http://your_username.github.io
...
...
title="This first blog"
theme="cactus"
```
Some time, in the index page, instead of show the full article, you just want the summary listed for all of the aricles. So the index page won't look lenghy to stroll from one article to another. To achieve this, just simply put tag "<!--more-->" after the abstract content and hugo will just show the content before "<!--more-->" and a "Read More ..." button in the index page.

## Host the webpage source code on github.com
Github is a free and nice place where a lot of open source software are hosted. And you could share your blog code there too!

Of course you need to first register an account on github.com. And then in the web interface choose to create a new repo: "+New repository" in a green button. Suppose we create a repo called blogstie.

### Start to record local changes
Go to "blogsite" folder, and run:
`git init`
This is to initiate a hub and let git start monitering the changes in this folder.

Then in the blogsite, setup an empty ASCII file called ".gitignore". Since this file has "." at the front which means it has a hidden attribute in linux, it is difficult to create in windows. What you could do is downloading the clint of Github and in the clint, under the "repository settings" you then choose "ignored files, add a default .gitignore file".


You could use notepad or nano to write following into this file:
`public/`
This is to tell git to ignore any changs in the public file, because we know that is the compiling results not the source code.

Now by running the following, you start to recored the first version of code:
```
git add .
git commit -m "The first trial blog." 
```

### Synchronize the changes with the remote Github Cloud
Next is to push the record of changes to the Github cloud, where the record could be safely stroed and synchronize between different machines if needed.

`git remote add origin https://github.com/Replace_with_YOUR_Username/blogsite`

This is to tell git which two folders, the local one and the remote one are to be connected together.

Then we upload, or called "push" the local record to the remote. This is done by:
`git push -u origin master`

Next time, After you finish any changes, you need to go through the whole process again to keep track of the changes using git.

When you have a new machine to work on, simple using:
`git clone https://github.com/your_username/blogsite`
then you will have another full version of the source code of your blog.

## Host the hugo output(webpage to show) on github.com

The idea will be the same as the last one, except this time we are going to push the content under "public/" folder to github.com.

Github could automatically power the html file when:
*  The name of the repo is "username.github.io";
*  There is a "index.html" in the root of the repo;

Again, we could first setup a repo with the name "username.github.io" and then connect the local "public" folder to the remote one and the do "push".

However, there are two ways to do that. The first one is to use the same way as we used before. We setup a seperate hub to monitor the changes in the "public/" folder and work seperately with the source code repo. 

Another way is to add "public/" folder as a submodule to the source code repo. Then when you need, you could synchronize the "public/" folder by running:
` git submodule update --recursive`

Imaging your software have several dependencies with other libraries or software. Instead of synchronizing them one by one, you could just run the above  git command, then all of the submodules will be updated locally.

Actually, it is also a good idea to register the "themes/cactus" as a submodule, so you could always have an updated themes.

Therefore We used the second way. In the "blogsite" folder:
`git submodule add -f git@github.com:your_username/your_username.github.io.git`

You can check if this is successful by:
`git submodule status`

Now we go to "public/"
```
git init
git add -A
git commit -m "The first html version"
git remote add origin https://github.com/your_username/your_username.github.io.git

git push origin master
```

Now, after about several miniutes, the blog will show up at: http://your_username.github.io

Remember to change the "baseurl" and the "title" in config.toml in the "blogsite" folder.

When you need to move to a new machine, after downloading the source code ("git clone https://github.com/your_username/blogsite "), you then could run:

`git submodule update`

to have the public folder to fill in and themes files up-to-date.






