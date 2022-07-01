---
layout: post
title: Use Visual Studio extension to distribute your Code Snippets
date: 2018-11-18 22:55:49 UTC
updated: 2018-11-18 22:55:49 UTC
comments: false
categories: extensions testing unit tests Visual Studio VSIX
---

## Introduction

In a previous post we saw how you can build code snippets to write tests faster empowering your team to follow good practices. [https://www.albertocorrales.com/2018/10/writing-unit-tests-faster.html](https://www.albertocorrales.com/2018/10/writing-unit-tests-faster.html)

In this post, we will see how we can distribute our snippets with a Visual Studio extension, which offers different advantages, for example:

- You encapsulate your snippets in a VS extension, you you don't need to distribute them independently, and developers don't need to add them to VS one by one.
- The extension can be updated improving he current snippets or including new snippets.
- You can add more functionality in your Visual Studio extension.

## Creating a new Visual Studio extension

First of all, if you have never developed a Visual Studio extension, you might need to install Visual Studio extension development kit. To do it, install this option in your Visual Studio Installer

[![](https://2.bp.blogspot.com/-Q-J6ccU0_CA/W_HHgcyr48I/AAAAAAAAFBo/sMZQu0jPTkkpNadUI6KcGVKziAel4WZfQCLcBGAs/s640/1.PNG)](https://2.bp.blogspot.com/-Q-J6ccU0_CA/W_HHgcyr48I/AAAAAAAAFBo/sMZQu0jPTkkpNadUI6KcGVKziAel4WZfQCLcBGAs/s1600/1.PNG)

Create a new Visual Studio extensions project. For this example, I will name it "SnippetsExtension":

[![](https://2.bp.blogspot.com/-Lfnybv_dJGU/W_HARGeQ7tI/AAAAAAAAFBA/zXKR8RKw9HQfJ2sVSfkblt8dlE9z3yTxgCLcBGAs/s640/2.JPG)](https://2.bp.blogspot.com/-Lfnybv_dJGU/W_HARGeQ7tI/AAAAAAAAFBA/zXKR8RKw9HQfJ2sVSfkblt8dlE9z3yTxgCLcBGAs/s1600/2.JPG)

In the root of the project, create a new folder named "snippets". Here you can store all the snippets you wan to distribute with your Visual Studio Extension. For this example, we will used the unittest.snippet example that we saw in the previous post. You can download it here: [https://github.com/albertocorrales/unittestsnippet/blob/master/unittest.snippet](https://github.com/albertocorrales/unittestsnippet/blob/master/unittest.snippet).

For file unittest.snippet, it is important to select the following properties:

- Build Action: Content
- Copy to Output Directory: Copy always
- Include in VSIX

[![](https://1.bp.blogspot.com/-jYXVNEWoG54/W_HARN_YQ6I/AAAAAAAAFA4/1bZbi1o0eXoGvwbaD6JiiGDfRQQkbFGoQCLcBGAs/s320/3.JPG)](https://1.bp.blogspot.com/-jYXVNEWoG54/W_HARN_YQ6I/AAAAAAAAFA4/1bZbi1o0eXoGvwbaD6JiiGDfRQQkbFGoQCLcBGAs/s1600/3.JPG)

In folder snippets, we will create a new text file and we will name it **snippets.pkgdef**. This file is used to add certain keys to the registry. As for this example, we are using CSharp, write the following:  
\-----------------------------------------------------------------  
// Csharp  
\[$RootKey$\\Languages\\CodeExpansions\\Csharp\\Paths\]  
"snippets"="$PackageFolder$"

\-----------------------------------------------------------------

For file snippets.pkgdef select the following properties:

- Build Action: Content
- Copy to Output Directory: Copy always
- Include in VSIX

If you followed the previos steps, you should see something like this:

[![](https://1.bp.blogspot.com/-mVlSFJj2dk0/W_HARk7xOfI/AAAAAAAAFBE/cnLIUE4WtOgaNnx2zjpODOJzWTscTYapwCLcBGAs/s320/4.JPG)](https://1.bp.blogspot.com/-mVlSFJj2dk0/W_HARk7xOfI/AAAAAAAAFBE/cnLIUE4WtOgaNnx2zjpODOJzWTscTYapwCLcBGAs/s1600/4.JPG)

Now add your package definition in your VSIX manifest. To do it, double click on **source.extensionvsxmanifest**. Go to tab **"Assets"**, and then, select the following options to distribute your snippets with your extension:

[![](https://3.bp.blogspot.com/-F5uND6G_rKM/W_HASFOQHEI/AAAAAAAAFBI/5LxY2cI1HsYNtbJ2GQQoOf_paoT79WBRgCLcBGAs/s1600/5.JPG)](https://3.bp.blogspot.com/-F5uND6G_rKM/W_HASFOQHEI/AAAAAAAAFBI/5LxY2cI1HsYNtbJ2GQQoOf_paoT79WBRgCLcBGAs/s1600/5.JPG)

Finally, test your extension. If you press F5, an experimental instance of Visual Studio will raise, the changes you make in this instance will not affect your development environment. To test your extension is working fine, create a new test project and type "utest" to see the new snippet you added.

[![](https://1.bp.blogspot.com/-zsdVficzu6Y/W_HASVf8sPI/AAAAAAAAFBM/x7WhnWFWtb8ugxWIn6M-GVcGQTq-JKifwCLcBGAs/s1600/6.JPG)](https://1.bp.blogspot.com/-zsdVficzu6Y/W_HASVf8sPI/AAAAAAAAFBM/x7WhnWFWtb8ugxWIn6M-GVcGQTq-JKifwCLcBGAs/s1600/6.JPG)

You can download this example from my GitHub repository. [https://github.com/albertocorrales/SnippetsExtension](https://github.com/albertocorrales/SnippetsExtension)

If you want more information about how to develop your own Visual Studio extensions, you can go here: [https://docs.microsoft.com/en-us/visualstudio/extensibility/starting-to-develop-visual-studio-extensions?view=vs-2017](https://docs.microsoft.com/en-us/visualstudio/extensibility/starting-to-develop-visual-studio-extensions?view=vs-2017)

I hope you found this post useful,

Happy coding!
