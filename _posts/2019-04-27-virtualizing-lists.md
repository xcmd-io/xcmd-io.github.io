---
layout: post
title: Virtualizing Lists
date: 2019-04-27 18:37:00 -0700
categories: blog
---

Performance is one of the main features of Cross Commander. When I was playing around with it in directories with large number of files, I noticed that it takes too long to show their content. The directory `C:\Windows\System32` on my system contains approximately 4,600 files and it took around eleven seconds to show its content. If I started the commander with both panes being set to this large directory, it took twice as much. Waiting more than twenty seconds is obviously unacceptable. For now, the implementation is synchronous, but it should be much faster even in this case.

Retrieving the list of 4,600 files took around 800-900 ms, the rest was spent elsewhere. Looking into it a bit more revealed that the slowness is caused by too many elements that have to be rendered in the file list. This is a common problem that has a common solution - virtualization. I wanted to implement virtual lists at some point, but I expected it to happen later. However, the large directories took so much time that improving this became immediately the top priority.

So how does the virtualization work? Instead of rendering all the rows, I render only the rows that are visible. This of course affects what the scrollbar shows. Therefore, I had to add a separate scrollbar and set its properties so it shows as if there were all the rows in the table. When scroll position changes, the virtual table retrieves only the data rows that are visible and shows them. There is opportunity to optimize this a bit more and modify only rows that become visible, but for now I am leaving it as it is.

While trying to find out what is taking time when listing large directories, I have also used Visual Studio's CPU profiler and I can tell you that it is an awesome tool. You can run a process and attach CPU profiler to it. It then shows you a report with functions and time it took to execute them. Thanks to this, I was able to identify another ineffectiveness. When listing the files, I was first checking whether it is a directory and then later retrieving its metadata to get the creation time. This resulted in retrieving metadata twice. By reading the metadata first and then using the file type information from there, I was able to reduce time to half, so now the listing takes only 450 ms for this large directory.

![CPU Profiler](/images/2019-04-27-cpu-profiler.png)

With this, the core functionality of Cross Commander is starting to be good enough to start implementing real functionality. Just kidding, there is still lot of things to implement in the core, but we are getting closer!
