---
layout: post
title: Another Way to Define Node.js Objects
---

In [my last post](/2010-04-05/understanding-nodejs-require.html) I discussed different ways to define objects using the [CommonJS](http://commonjs.org/) module system as implemented in [Node.js](http://nodejs.org/). There's one particular example I wanted to revisit:

```javascript
var Circle = function (radius) {
	this.radius = radius
}

Circle.PI = 3.14

Circle.prototype = {
	area: function () {
		return Circle.PI * this.radius * this.radius
	}
}

exports.Circle = Circle
```

I had decried this form as awkward as illustrated by this scenario:

```
node> var circle = require('circle_object')
{ Circle: { [Function] PI: 3.14 } }
node> var c = new circle.Circle(2)
{ radius: 2 }
node> c.area()
12.56
```

I was reading some of the source code for the new [0.1.90 release of Node](http://groups.google.com/group/nodejs/browse_thread/thread/b6e0165f983d1f08), and I saw an example that made me realize we're using this object wrong. Here's a better way to use the above definition:

```
node> var Circle = require('circle_object').Circle
{ Circle: { [Function] PI: 3.14 } }
node> var c = new Circle(2)
{ radius: 2 }
node> c.area()
12.56
```

Look at that! We've got traditional style objects without polluting the global object.
