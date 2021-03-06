---
layout: post
title: Script to clean your Visual Studio solution folder
date: 2018-12-09 11:24:14 UTC
updated: 2018-12-09 11:24:14 UTC
comments: false
categories: clean projects cmd script tools useful
---

[![](https://1.bp.blogspot.com/-yfFVQS13eL8/XAz7Q6AM9DI/AAAAAAAAFGc/wFqdWxyLpZsIUeWNbhEkI-doC9GHyibHgCLcBGAs/s640/professional-cleaning-tips.jpg)](https://1.bp.blogspot.com/-yfFVQS13eL8/XAz7Q6AM9DI/AAAAAAAAFGc/wFqdWxyLpZsIUeWNbhEkI-doC9GHyibHgCLcBGAs/s1600/professional-cleaning-tips.jpg)

Sometimes, cleaning your solution folder comes in handy when you want to build from scratch. If you don't do so, you can have unexpected results because of the files generated by your previous builds.

To carried out the cleaning task, I use a simple script which allows me to configure a list of folders I want to remove. This script is divided in two parts. The first part is just a script to check if we have admin permissions. If not, it will display a pop-up to get admin permissions.

[![](https://1.bp.blogspot.com/-RdBVSLN4AWc/XAzoRSxO0TI/AAAAAAAAFGQ/drKIEflyx1wAQJO0WbbM6nLcb6ywdWxswCLcBGAs/s320/cpa_2.png)](https://1.bp.blogspot.com/-RdBVSLN4AWc/XAzoRSxO0TI/AAAAAAAAFGQ/drKIEflyx1wAQJO0WbbM6nLcb6ywdWxswCLcBGAs/s1600/cpa_2.png)

You can skip this part, but it is very handy, since the cleaning part might need admin permissions. This part can be very useful for all your scripts, which need admin permissions.

    :: BatchGotAdmin:-------------------------------------REM  --> Check for permissions    IF "%PROCESSOR_ARCHITECTURE%" EQU "amd64" (>nul 2>&1 "%SYSTEMROOT%\SysWOW64\cacls.exe" "%SYSTEMROOT%\SysWOW64\config\system") ELSE (>nul 2>&1 "%SYSTEMROOT%\system32\cacls.exe" "%SYSTEMROOT%\system32\config\system")REM --> If error flag set, we do not have admin.if '%errorlevel%' NEQ '0' (    echo Requesting administrative privileges...    goto UACPrompt) else ( goto gotAdmin ):UACPrompt    echo Set UAC = CreateObject^("Shell.Application"^) > "%temp%\getadmin.vbs"    set params= %*    echo UAC.ShellExecute "cmd.exe", "/c ""%~s0"" %params:"=""%", "", "runas", 1 >> "%temp%\getadmin.vbs"    "%temp%\getadmin.vbs"    del "%temp%\getadmin.vbs"    exit /B:gotAdmin    pushd "%CD%"    CD /D "%~dp0":--------------------------------------

The second part of the script, is the cleaning part, where the script will remove recursively all the folders you configure, from the current path to all the subfolders. In my case, I usually remove BIN, OBJ and PACKAGES folders, so this is my script:

    :: Remove folders bin, obj and packages@echo Deleting all BIN, OBJ and PACKAGES folders…for /d /r . %%d in (bin,obj,packages) do @if exist "%%d" rd /s/q "%%d"@echo BIN and OBJ folders successfully deleted. Close the window.pause > nul

You can find the .bat on my GitHub: [https://github.com/albertocorrales/script-clean-sln/blob/master/clean_solution.bat](https://github.com/albertocorrales/script-clean-sln/blob/master/clean_solution.bat)

I hope you found this post useful,

Happy coding :)
