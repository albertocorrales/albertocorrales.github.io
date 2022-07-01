---
layout: post
title: Run Visual Studio as Administrator
date: 2018-11-09 13:53:52 UTC
updated: 2018-11-09 13:53:52 UTC
comments: false
categories: dev tip tips & tricks Visual Studio
---

Probably you already knew this trick to run apps in Windows 10 as Administrator. If not, I hope this tip can help you.

Sometimes, you need to execute Visual Studio as Administrator always, for example if you are deploying and debugging your application in your Local IIS. To execute VS as Admin there are several ways.

**1) Using Sub-menus:**  
If you go to the item in Windows menu and click on right, you will see menu "More", where you can run VS as administrator.

[![](https://2.bp.blogspot.com/-iGm23mgVI-0/W-WP50ynp1I/AAAAAAAAE-A/mcEyA-qkueAyHo6xN3Zq9Ipu4o6nim04ACLcBGAs/s400/pastedImage_1.png)](https://2.bp.blogspot.com/-iGm23mgVI-0/W-WP50ynp1I/AAAAAAAAE-A/mcEyA-qkueAyHo6xN3Zq9Ipu4o6nim04ACLcBGAs/s1600/pastedImage_1.png)  
**2) Using shortcuts:**  
Previous method is slow, so If you don't want to do this each time you open VS, you can create a shortcut in your Desktop and execute it as Administrator. To do this, press right click on the shortcut, click on Advanced and select Run as administrator.

[![](https://1.bp.blogspot.com/-rxdeOYmpYK4/W-WP54U8ZyI/AAAAAAAAE-I/RAAON0XM308RaDnDNJfJsPcKRvnGRHwCACLcBGAs/s640/pastedImage_3.png)](https://1.bp.blogspot.com/-rxdeOYmpYK4/W-WP54U8ZyI/AAAAAAAAE-I/RAAON0XM308RaDnDNJfJsPcKRvnGRHwCACLcBGAs/s1600/pastedImage_3.png)  
**3) Always as Administrator:**  
Method 2 is much better than 1, but you always have to open VS by using your modified shortcut. So for example, you can not open directly a file .sln with Admin rights. But don't worry, there is a method to execute VS always as Administrator.  
In the properties of the previous shortcut, go to "Open File Location". Over devenv.exe press right click and go to Troubleshoot compatibility.  
[![](https://2.bp.blogspot.com/-mh5mrPBc7f4/W-WP56iN9_I/AAAAAAAAE-E/9gOIJXHVQy0OlQiXV7mLHlZkgCMqInyDwCLcBGAs/s1600/pastedImage_4.png)](https://2.bp.blogspot.com/-mh5mrPBc7f4/W-WP56iN9_I/AAAAAAAAE-E/9gOIJXHVQy0OlQiXV7mLHlZkgCMqInyDwCLcBGAs/s1600/pastedImage_4.png)

Then choose Troubleshoot program and select "The program requires additional permissions".  
Finally, save these settings for this program and you won't have to care anymore about open VS with sub menus or shortcuts to run it as Administrator. You can use the same method for PowerShell or whatever program you want.  
[![](https://2.bp.blogspot.com/-Hod2tEPEIGw/W-WP6WcCUnI/AAAAAAAAE-M/jBbEen-1OycSrzXwmQL8_Qzj6xBMkBItQCLcBGAs/s400/pastedImage_5.png)](https://2.bp.blogspot.com/-Hod2tEPEIGw/W-WP6WcCUnI/AAAAAAAAE-M/jBbEen-1OycSrzXwmQL8_Qzj6xBMkBItQCLcBGAs/s1600/pastedImage_5.png)

I hope you find this tip useful.

Happy coding.
