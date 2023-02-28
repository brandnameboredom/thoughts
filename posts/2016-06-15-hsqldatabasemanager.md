---
title: HSQL Database Manager
description: HSQL Database Manager
date: 2016-06-15
tags:
  - Liferay
  - HSQL
---

Recently I've been working with the [Liferay] platform and I found myself in a situation where I wanted to inspect the data stored in the [Hypersonic] database that comes bundled with Liferay.  I found two recommended products for inspecting the database: 

  * [SquirrelSQL]
  * [HSQL Database Manager]

The Liferay wiki already has a pretty decent post detailing how to setup SquirrelSQL to interact with the database so I will focus entirely on what I had to do to get the HSQL Database Manager to connect to my local database instance.

First, I had to locate my hsql.jar file.  For me, that was in the ext folder under my tomcat instance:

> %LIFERAY_HOME%\tomcat\lib\ext\hsql.jar

Next, I ran the following command:

> java -cp %LIFERAY_HOME%\tomcat\lib\ext\hsql.jar org.hsqldb.util.DatabaseManagerSwing

This launched the HSQL Database Manager:

<img src="https://cloud.githubusercontent.com/assets/3187885/16093499/21673dba-330a-11e6-8e7b-b3229c1e44c5.png" alt="HSQL Database Manager" />

Finally, I supplied my database URL:

> jdbc:hsqldb:file:%LIFERAY_HOME%\data\hsql\lportal

Click "Ok" and...

<img src="https://cloud.githubusercontent.com/assets/3187885/16093500/216df1d2-330a-11e6-897d-5b684eddfaad.png" alt="HSQL Database Manager Connected" />

##### NOTE: For more detailed information on the Database Manager tool and all of it's capabilities please check out the [official documentation]. 

[HSQL Database Manager]: http://hsqldb.org/doc/2.0/util-guide/dbm-chapt.html
[official documentation]: http://hsqldb.org/doc/2.0/util-guide/dbm-chapt.html
[Liferay]: https://www.liferay.com/
[pretty decent post]: https://web.liferay.com/community/wiki/-/wiki/Main/HSQLInspect
[SquirrelSQL]: http://squirrel-sql.sourceforge.net/
[Hypersonic]: http://hsqldb.org/