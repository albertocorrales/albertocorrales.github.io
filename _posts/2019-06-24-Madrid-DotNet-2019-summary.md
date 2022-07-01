---
layout: post
title: Madrid DotNet 2019 summary
date: 2019-06-24 18:14:50 UTC
updated: 2019-06-24 18:14:50 UTC
comments: false
categories: 2019 dotnet dotnet2019 events microsoft
---

[![](https://1.bp.blogspot.com/-wYgUDN2FroQ/XQ1iK2EGGkI/AAAAAAAAFl0/eqAUQLYDDtMMdnAwi6dEAWGKmww_PLHSwCLcBGAs/s320/dotnet.PNG)](https://1.bp.blogspot.com/-wYgUDN2FroQ/XQ1iK2EGGkI/AAAAAAAAFl0/eqAUQLYDDtMMdnAwi6dEAWGKmww_PLHSwCLcBGAs/s1600/dotnet.PNG)

Thanks to Fenergo, this week I had the chance to attend DotNet 2019 in Madrid and it was a really rewarding experience. For this reason, I would like to post a summary of the day and the key point of the speeches I could attend.

First of all, the event started with a **keynote by the Scott Hunter**. He talked about the innovations incoming to the dotnet world. Some of the key points mentioned were: the introduction of Blazor, ML.net, c# 8.0, .net core 3.0 recent features like performance improvement and the future .NET 5, which will the only one version for .net to support Windows, Linux, macOS, iOS, Android, tvOS, watchOS and WebAssembly, etc.

[![](https://1.bp.blogspot.com/-33xo68JV5do/XQ-ixMtnUmI/AAAAAAAAFmE/dJLFImaNEfMC9KUTRUTxZpAUBXhFAmTSACLcBGAs/s640/0.jpg)](https://1.bp.blogspot.com/-33xo68JV5do/XQ-ixMtnUmI/AAAAAAAAFmE/dJLFImaNEfMC9KUTRUTxZpAUBXhFAmTSACLcBGAs/s1600/0.jpg)

Next, I attended the session **"SSR with typescript, React and NextJS" by Carlos Bastos Pérez**. In this session, Carlos showed how he does an analysis to choose a technology stack. In the case of NextJS, it allow us to build server side render react applications easily, which can be a really suitable solution when we need to support SEO in our SPA or to support browsers which don't support javascript. In addition, this can also provide performance improvement depending on the scenario. Regarding typecript, Carlos mentioned how it can be the key to produce code more maintainable and to reduce the amount of bugs in our code. Carlos also recommended the package typestyle, which allow us to type our css, so we don't end up with mess of classes repeated in our code. Here we have the example of the demo for this session: [https://github.com/cbastos/dotnet-2019-ssr-typescript-react-nextjs](https://github.com/cbastos/dotnet-2019-ssr-typescript-react-nextjs)

In the following session, **"Asp.Net Core Good practices", Carlos Landeras and Luis Ruiz Pavón** talked about different techniques to improve our dotnet applicaitons, such as code instrumentation, resilience, health checks, performance improvements with HttpContext Pipes, Endpoint routing, etc. In addition, they have shared a repository with examples of all these good practices here: [https://github.com/CarlosLanderas/dotnet2019-aspnet-core-best-practices](https://github.com/CarlosLanderas/dotnet2019-aspnet-core-best-practices)

In next session, **"Introduction to Blazor", Ryan Nowak** showed the current state of Blazor, the new dotnet framework based on Web Assembly, which will allow us to develop single page applications in C#. During the presentation Ryan showed examples of client and sever rendering with Blazor.

[![](https://1.bp.blogspot.com/-YiU3LpbYTaw/XQ-k1-DGXFI/AAAAAAAAFmQ/_Ghjelht8l84fvxpfpwf-Ahl3BVysNXWgCLcBGAs/s640/IMG_20190619_150154.jpg)](https://1.bp.blogspot.com/-YiU3LpbYTaw/XQ-k1-DGXFI/AAAAAAAAFmQ/_Ghjelht8l84fvxpfpwf-Ahl3BVysNXWgCLcBGAs/s1600/IMG_20190619_150154.jpg)

In the next presentation, **"Improving Vue with typescript, inversify and vuex", Quique Fdez Guerra** talked about the benefits of using technologies like typescript or inversify in our frontend application, in this case Vue. Quique highlighted how, apart from the technology used, typescirpt and inversify can help you to produce maintainable code based on dependency inversion and other patterns and good practices.

Last but not least, **Hugo Biarge presented "Authentication in native and web applications"**, where he talked about different authentication scenarios, like: Local website, Applications using active directory and Applications using a OIDP as a Federation Gateway. The repository with the presented examples is here: [https://github.com/hbiarge/authentication-samples](https://github.com/hbiarge/authentication-samples)

In conclusion, Dotnet 2019 was a really interesting conference, where I had the chance to learn new things and meet awesome people. I'm really looking forward to Dotnet 2020, which will be an event event greater, as Pablo Pérez (the CEO of Plain Concepts) announced.

I'm also preparing some posts related to the technologies presented, as there are some technologies I would like to investigate further. So, if you are interested in web development, stay tuned.

Happy coding!
