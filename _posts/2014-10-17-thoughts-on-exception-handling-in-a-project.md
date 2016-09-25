---
id: 23
title: Thoughts on Exception Handling in a Project
date: 2014-10-17T00:00:00+00:00
author: Jorden
layout: post
guid: http://www.fbombcode.com/title/Thoughts_on_Exception_Handling_in_a_Project
permalink: /2014/10/17/thoughts-on-exception-handling-in-a-project/
categories:
  - general
tags:
  - exceptions
  - general
---
 <p> With a new project, knowing how to handle exceptions is paramount. I have been thinking hard about what are the basic exceptions that are out there, and what you should do with those exceptions. </p> <p> So, in thinking about exception handling, there are 3 types of resources that come to mind; File, Database and Web Service. With each, there are only a few exceptions to think about: </p> <ol> <li>Offline</li> <li>Permissions</li> <li>Data schema</li> </ol> <p> On top of that, it is important you build in retry logic, if needed, as well as transactions. This can complicate a workflow, if you don&#8217;t build this in correctly. Retry logic might be closer to the call, than the exception handling. Which means you have potientially 2 spots for exceptions, one at the function call, and one where you catch wider exceptions. The transaction exceptions may be in the wider exceptions too, but make sure you use a design pattern to hand it appropriately, like Command Pattern mixed with a Memento pattern. </p> <p> Also, something else to consider, is how often you alert. You may not want to alert EVERYTIME, but roll alerts every 15 minutes. This can be quite useful when a database is offline. Thoughts on this is to retry database every 15 minutes and if it is offline, maybe put the program/service to sleep till it can do what it needs to do. For a web service, this is something a message queue and polling statuses would fix. All in all, dont alert someone about every exception, especially if it is for the same underlying issue. </p> <p> Going back to the 4 types of resources, here are some things you can catch to allow you to simplify your exception handling: </p> <h2> File, things to catch </h2> <ol> <li>FileNotFoundException, DirectoryNotFoundException &#8211; Offline</li> <li>UnauthorizedAccessException &#8211; Permissions</li> <li>Everything else &#8211; Data schema</li> </ol> <p> In file access, there are some easy exceptions to find. FileNotFound and DirectoryNotFound Exceptions can tell you a file is not online if a file system is done. One concern is that if you are generating files, then make sure that file was generated. UnauthorizedAccessException will tell you that the permissions are not set, and this one, hopefully, is a one time exception. For all other exceptions, at least for now, would result in data schema issues. </p> <h2> SQL, things to catch </h2> <ol> <li><a href="http://msdn.microsoft.com/en-us/library/cc645603.aspx">SqlException</a></li> <li>below 20 &#8211; Offline</li> <li>218, 219, 229, 230, 262, 297, 300 &#8211; Permissions</li> <li>Everything else &#8211; Data schema</li> </ol> <p> Sql exceptions are dubious. They require you to use one exception! But that exception has lots of error codes. If you check for exceptions below 20, then chances are these are offline. If you are looking for permissions issues, then 218, 219, 229, 230, 262, 291, 300 are the exceptions you are looking for. Lastly, any other error is more data/schema related. </p> <h2> HttpClient, things to catch </h2> <ol> <li>AggregateException</li> <li>WebException should be in the AggregateException &#8211; Offline</li> <li>No Error! look for a 401 &#8211; Permissions</li> <li>Everything else &#8211; Data schema</li> </ol> <p> HttpClient has been awesome to use, but not awesome for exception handling. First off, use WebException, which will be in the AggregateException, will tell you that a resource is offline. For permissions, you need to look at the HttpResponseMessage for a 401 Http Status code, because it is still considered a &#8220;valid&#8221; request. All other exceptions are considered business logic issues. </p> <p> For each exception, make sure you add what you were accessing, the user that was accessing it with and the underlying exception. You should be good with that information to help resolve the issues fast. Make sure these exceptions go to the right people too, cause a developer is not going to be able to restart a server if they are not an admin. For more lists of exceptions for other resources, check out a previous blog article of mine: <a href="/title/Exception\_Handling\_and\_What\_To\_Do">/title/Exception\_Handling\_and\_What\_To\_Do</a>. </p> <p>Any other exception handling tips, please leave them in the comments.</p>