+++
date = "2018-10-21"

title = "VS code for building CC++ code using task.json 2.0.0"

description = ""

categories = ["Development", "CC++",]

tags = [ "CC++", "VScode",]

image = "image.jpg" # optional
toc = true # optional, When set to TRUE this parameter, table of contents appears in only this article.

+++


Vistual studio code is a modern UI using electron. It is like Vim/ Emacs that just focus on user interface. Then, if you want to use it as the IDE for certain programing language, like python, C, C++, javascript etc, you will need to configure it youself. And VS code become more popular after it has been the IDE coming with the Anaconde python package (Yes, it VS code looks better than spyder). 

The idea behind making a general UI instead of developing a specified IDE for C/C++ (like early version of VS studio), is like the recent software architecture idea that frontend and backend of a website should be decoupled for a better maintainence/refactoring  in the future, However, this also brings some confusions in how to configurate VS code for C/C++ compling using task.json. Things become worse when they recently upgrade the format of task.json to 2.0.0 while most examples you found on the web were still task.json 0.1.0.

## Task
VS code is not designed just as a complier IDE. Therefore, it has to generalize the concept of "build", "run", etc. VS code chose the name "task". Every function, like "build", which before is a button-click, now is a "task". It is lucid to understand, using the name task can represent more action for other applications. However, to define a task, VS code uses "\*.json" file. 

As to our purpose speaking, there are two json files involved: "task.json" and "launch.json". The first one is to build and run a code and the latter is for debugging purpose (Yes,launch is not for exacuating the code.).

I was first exposed to such an idea in using UltraEdit. You can program some macro and then put a new "button" in its menu. What we are going to do is actually the same, except we won't put a button in the menu explicitly.

## Task.json 2.0.0
Json, or Javascript Object Notation is a file format that is very popular among frontend programing, as you can guess out by its name. Since it is written in ASCII code, it is easy to read and maintain as well. Now, many software, especially those using javascript framework (like electron) to achieve cross-plateform, uses JSON a lot. So you can put some parameters for your program in a JSON file for program to use, or a macro to deliver certain function. Recently, VS code team upgrade their parameters set in JSON to 2.0.0. There are some big changes.

## Steps
Here is we need to do. 
- Of course, first step is to install a compiler, I used TDM-GCC 5.1 since I run it in windows. Then install the VS code extention: Press Ctrl+Ship+P to open command palate, and input"ext install" to filter out other commands and choose "ext install extention". then a side panel on the left part will show up. Search for "C/C++" extention from Microsoft and install it. You will need to reload after that. 

- In the VS code, you use "open folder" to open the folder that has the C/C++ source code. This will setup a folder ".vscode" that contains two files: "task.json" and "launch.json". 

- open "task.json" and edit:
```json
{   //
    "version": "2.0.0",
    "tasks": [
        {	//the name showed in the command palate
            "label": "g++ build only",
            "command": "g++",
            
            //for more environment parameters, see "https://code.visualstudio.com/docs/editor/variables-reference"
            "args": [
                "-g",
                "-std=c++11",
                "${file}",
                "-o",
                "${workspaceRoot}\\${fileBasenameNoExtension}.exe"
            ],
            "type": "shell",
            "group": {
                "kind": "build",
                "isDefault": true
            }
        },
        {
            "label": "run code after building",
            "presentation": {
                "reveal": "always"
            },
            "command": "./${fileBasenameNoExtension}.exe",
            "type": "shell",
        }
    ]
}
```

There are some differences from version task.json 0.1.0: task name change to label, fileBasename no long works (it will still give the entire filename), to show the output window, you now use "prsentation, reveal" not "showOuput", and "group" to replace many "isDefault". So if you follow the example on the internet, which are mostly task.json 0.1.0, they won't function properly.

- After "tasks.json" is done, you should be able to find the names of two labels in the menu "Terminal" ==>"Run task...".  Then switch to the C/C++ source file and choose corresponding tasks you want to perform.

