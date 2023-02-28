---
title: "[FromQuery] collection type fail"
description: "[FromQuery] collection type fail"
date: 2018-08-02
tags:
  - dotnetcore
  - api
  - attribute
  - controller
  - collections
---

Ran into this issue today and wanted to document it here in case I stumbled upon it again.  I have a very simple API controller with a GET method to retrieve detailed information on some objects from the data store.  The method accepts a collection of id values.  Here is the API class and method signatures:

```
[Route("api/[controller]")]
[ApiController]
public class ItemController : Controller
{

  [HttpGet]
  public string GetDetails([FromQuery]int[] ids)
```

I was using Insomnia Rest to make a GET request to my controller using the following URL value:

```
localhost:5000/api/item/getdetails?ids=1&ids=2&ids=3
```

Sweet.  Good to go.  Click "Send" in Insomnia and watch my glorious integer array pass along my values and filter out my data.  

Nope.

My integer array has a length of 0.

Wait. What? How? 

![No. That's not true. That's Impossible!](https://media.giphy.com/media/3ornk6UHtk276vLtkY/giphy.gif)

Turns out there [is a bug].  Apparently, the ```[ApiController]``` attribute breaks the ```[FromQuery]``` attribute for collection types in the version of dotnetcore I'm using.  

Remove the ```[ApiController]``` attribute and all is well again.

Or 

Specify the parameter name in the ```[FromQuery]``` attribute like ```[FromQuery(Name="ids")]``` and all is well again.

Oh, in case you are wondering.  I'm currently running:

Dotnet Version 2.1.301 Commit 59524873d6

[Insomnia Rest]: https://insomnia.rest/
[is a bug]: https://github.com/aspnet/Mvc/issues/7712
