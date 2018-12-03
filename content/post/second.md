+++
Categories = ["Development"]
Description = ""
Tags = ["Hugo", "Golang"]
date = "2016-01-21T22:59:16-06:00"
title = "Adding a comment system to the hugo blog"
+++


One of the shortcoming of static blog system is they usually don't have a comment system. But good news is there are many 3rd party comment systems available, one of which is Disqus.

<!--more-->

To use Disqus, you have to register at their website and get a user name to use.

At the root folder of themes, set up a file "config.toml" if you don't see one. This file usually contains the author's information. 

Adding following line to let disqus connected to you account.

```
disqusShortname = "YourDisqusUserName"
```

And the comment system will be added.