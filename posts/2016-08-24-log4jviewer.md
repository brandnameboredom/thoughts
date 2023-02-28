---
title: Log Viewers for Log4J
description: Log Viewers for Log4J
date: 2016-08-24
tags:
  - Chainsaw
  - Otros Log Viewer
  - Log4J
  - Liferay
---

I ran into a situation at work the other day where I wanted to disect some rather large [Log4J] log files.  Each of the files contained a bunch of text log entries of the following format:

> 13:20:32,243 INFO  [localhost-startStop-1][DialectDetector:71] Determine dialect

What I wanted was a tool to help visualize what was going on in these files.  After a quick search, I stumbled upon two free tools I thought may do the trick:

# [Chainsaw]
Chainsaw is "a companion application to Log4j written by members of the Log4j development community".  In Chainsaw, I had to create a new Receiver in order to process the .log file.  The receiver designates a "logFormat" property in which you can specify the pattern for each log message.  After some trial and error, these settings seemed to work:

```
 fileURL: file:\\\C:\<My Log Location>\<My log>.log
 filterExpression:
 logFormat: TIMESTAMP LEVEL [THREAD][CLASS:LINE] MESSAGE
 name: Custom Receiver
 tailing: false
 timestampFormat: HH:mm:ss,SSS
```

# [Otros Log Viewer]
Otros Log Viewer is an open source piece of software whose intention is to assist in the analysis of application logs and traces.  In order to establish a connection between Otros Log Viewer and the log file I was attempting to analyze, I needed to supply a definition for each log file entry.  To do this, I created a .pattern file in the plugins > logimporters directory which contained the text:

```
 type=log4j
 pattern=TIMESTAMP LEVEL [THREAD][CLASS:LINE] MESSAGE
 dateFormat=HH:mm:ss,SSS
 name=Lo4j-CustomReceiver
 charset=windows-1252
```

[Log4J]: http://logging.apache.org/log4j/2.x/
[Chainsaw]: https://logging.apache.org/chainsaw/
[Otros Log Viewer]: https://github.com/otros-systems/otroslogviewer