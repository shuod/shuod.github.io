+++
title = "Checking files in subfolders using power shell"
description = ""
tags = [
    "Shell_powershell",
    "My_script",
    "Windows"
]
date = "2017-07-31"
categories = [
    "My_script",
    "Development" 
]

image = "image.jpg" # optional
toc = false # optional, When set to TRUE this parameter, table of contents appears in only this article.
+++

As daily work, sometime I need to check some log files from certain calculations -- a large number of calculations. Usually they contain some keyword or sentence, like "Normal termination" or "Calculation ended" or "succussefully conveged" for me to search.

This is a typical job that can be done rountinely in linux using commands like "grep", "awk". However, after download those file to a windows PC, it would be more convinient to use windows shell to do, although there exists some "tranplanted" version of some of the linux shell commands that are complied with native win32 toolchains.

Compared to their linux "collegues", windows power shell commands usually with names that are easy to remember. I recoded a windows power shell script that I used to check the calculation completion of Gaussian09 output file. You will see my point at once (Yes, it has a spciallized GUI editor!):

![tdd-django-pycharm-3-1](/images/checking_files_in_subfolders_using-power_shell.png)


The code is as follows and its function is to output the last 3 lines in log files of guassian calculations that are distributed in subfolders in "f:\1_data_1000\*.out". I don't think i need to explain anything, names of commands are pretty much self-explained.

```

$files = Get-ChildItem -recurse "f:\1_data_1000\*.out"

for ($i=0; $i -lt $files.Count; $i++) {
    $outfile = $files[$i].FullName + "out" >> "F:\1_data_1000\g09_done_check.txt"
    Get-Content $files[$i] -Tail 3 >> "F:\1_data_1000\g09_done_check.txt"
    echo "=============================================================" >> "F:\1_data_1000\g09_done_check.txt"
}
```

If you want to run this script, remember the extention name is "*.ps1" instead of "*.bat". And you have to open a special windows power shell terminal or right click the file choosing run. If you run it in a windows power shell terminal, usually you have to include the "compiler":

PS> powershell -executionpolicy bypass "g09_tail_check.ps1"

where the "-executionpolicy bypass" is to let system give you a "green light" on running power shell script.

**Reference:**
1. How to run a PowerShell script?
https://stackoverflow.com/questions/2035193/how-to-run-a-powershell-script

2. Loop through files in a directory using PowerShell
https://stackoverflow.com/questions/18847145/loop-through-files-in-a-directory-using-powershell

3. Unix tail equivalent command in Windows Powershell
https://stackoverflow.com/questions/4426442/unix-tail-equivalent-command-in-windows-powershell




