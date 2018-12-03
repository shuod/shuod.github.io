+++
title = "scandir in python35"
description = ""
tags = [
    "python",
    "linux",
    "useful script",
]
date = "2018-08-12"
categories = [
    "Development",
    "python",
]

image = "image.jpg" # optional
toc = true # optional, When set to TRUE this parameter, table of contents appears in only this article.
+++

# Test of scandir in python35

As one of my daily works, checking files from simulation results is of course my practical need. Therefore, I am always interested in using different ways to do it. One of my previous post, "[checking_files_in_subfolders_using_power_shell](http://shuod.github.io/post/checking_files_in_subfolders_using_power_shell/)" was on how to use power shell in windows 10 to do this job. Here I am going to test other ways that I used even before powershell script.

### find

    \"find\" is a Linux command and it is actually my first way to iterate folders to find files, since the operation system I work with in Supercomputer is Linux.

```
    find  .  -name  '*.o'  -type  f  -print  -exec  rm  -rf  {} \;
```

## 2 bash
    In order to let the Supercomputer to execute your code, users want to use a job schedular system (one execution of your code on supercomputer was called a job). The job schedular system allows you to use a script to submit your job to it. Inside this submission script, you sometimes want to incoporate some operations on files. For example, if you want to sumbit 1000 jobs and read 1000 input files in several folders or some thing like this, you are unlikely to do this with manual input.

    Certainly, you need a script to the iteration job.

```
for file in `ls -1d *.gjf | sed 's/.gjf//g'`
do
	"some operations"

done
```

## 3 python3
    Using python is a completely personal choice. You could use perl or ruby. The point is Bash, in my opinion, is bad in dealing with numbers/math calculations.
    
    If you have some logic that invovle numbers/calculations, you will see how tedious it is. For example, if you want to extract a number from some file and take some digits from this number, then after doing some substraction, multiplation, division, you got another number, which decide how many files in certain folder you should submit with your code.

    You will find bash is not straighforward enough to carry out those calculations. So I chose python to do this job. You need 

```
for parent,dirnames,filenames in os.walk(start_dir):
    for filename in filenames:
        if os.path.splitext(filename)[1] == '.out':
#as a test to check the output
            print("filename with full path:"+ os.path.join(parent,filename))
#carry out some calculation             
#...
#...
```


## 4 python35
    In python 3.5, there is a new feature/improvement: [os.scandir](https://www.python.org/dev/peps/pep-0471/). They changed the way Python iterates over the folders. 

    According to the [PEP471](https://www.python.org/dev/peps/pep-0471/), the way beofre, which is "os.walk()", make unnecessary system calls, therefore slower than it should be. The new function "os.scandir()", after optimization, is "8-9 times as fast on Windows and 2-3 times as fast as on POSIX systems".

    So the new function has the [form] (https://www.python.org/dev/peps/pep-0471/)
```    
    def subdirs(path):
    """Yield directory names not starting with '.' under given path."""
    for entry in os.scandir(path):
        if not entry.name.startswith('.') and entry.is_dir():
            yield entry.name
#carry out some calculation 
# 

```            