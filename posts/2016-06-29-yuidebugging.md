---
title: Debugging AlloyUI
description: Debugging AlloyUI
date: 2016-06-29
tags:
  - Liferay
  - AUI
  - AlloyUI
  - YUI
---

[Liferay] uses [AlloyUI] for frontend development by default.  AlloyUI is a wrapper around [YUI].  With that said, all of the YUI development niceties are available for debugging AlloyUI code.  For instance, the console:

``` javascript
AUI().ready('aui-base', 'console', function(A) {
    new A.Console({
        height: '250px',
        newestOnTop: false,
        style: 'separate',
        visible: true,
        width: '600px'
    }).render();
});
```

Sweet.  Now, I can do things like:

``` javascript
AUI().use('aui-base', function(A) {
    A.log("This is some test that will log to the YUI console.");
});
```

[Liferay]: https://www.liferay.com/
[AlloyUI]: http://alloyui.com/
[YUI]: http://yuilibrary.com/