---
layout: post
title: Understanding Node.js' "require"
---

I've been playing with [Node.js](http://nodejs.org) lately. Node is an asynchronous, JavaScript library for building server-side applications that uses [CommonJS](http://commonjs.org/) conventions. One of these conventions, modules, has confused me for quite a while now. Let's take a closer look at them.

We're going to be using Node version 0.1.33 in this article. Let's get to it.

# The Basics

Let's build some functions to deal with circles. Here's some code you can find in the Node documentation:

```javascript
var PI = 3.14

exports.area = function (r) {
  return PI * r * r
}

exports.circumference = function (r) {
  return 2 * PI * r
}
```

Okay. Now let's use this in Node. If you're like me, your first session in Node will look something like this:

```
node> require.paths.unshift('.')
3
node> require('circle')
{ area: [Function], circumference: [Function] }
node> area
ReferenceError: area is not defined
    at EventEmitter.anonymous (eval at readline (/usr/local/lib/node/libraries/repl.js:48:9))
    at EventEmitter.readline (/usr/local/lib/node/libraries/repl.js:48:19)
    at node.js:845:9
```

## What Just Happened?

Coming from a Ruby background, I would have expected methods I required to be available to me. That's not the case with CommonJS modules. Let's take a look at how you should have used this module:

```
node> require.paths.unshift('.')
3
node> var circle = require('circle')
{ area: [Function], circumference: [Function] }
node> circle.area(5)
78.5
```

Success! We can now do what math that every grade four student can do. Let's try something else:

```
node> circle.PI
node>
```

Nada! Node doesn't spell it out, but we've just called a property of <tt>circle</tt> that doesn't exist. Let's verify:

```
node> typeof circle.PI == 'undefined'
true
node>
```

## Making Sense of Things

Is the above a little confusing for you? It sure was for me at first. If you read the Node documentation, you'll see a nugget that might provide a clue. Check this out:

<blockquote>
<p>To export an object, add to the special exports object. (Alternatively, one can use this instead of exports.)</p>
</blockquote>

So, we can rewrite our circle like this:

```javascript
var PI = 3.14

this.area = function (r) {
  return PI * r * r
}

this.circumference = function (r) {
  return 2 * PI * r
}
```

And what is <code>this</code>? Well, it's your object! So, we can think of this module as this:

```javascript
function circle () {
	var PI = 3.14

	this.area = function (r) {
  	return PI * r * r
	}

	this.circumference = function (r) {
  	return 2 * PI * r
	}
}
```

You're seen this before. It's a JavaScrip object. So modules are really nothing more than objects. As a short form, we're just defining the body of the object. Now we have a mental model of how CommonJS modules work. In fact, we'd use this object just like we used our circle functions with Node:

```
node> var c = new circle();
node> c.area(5)
78.5
```

# Where You're Probably Making Your Mistake (and I Did Too)

You, probably like me, come from an Object-Oriented background. Let's look at how we'd write our circle in my favourite language, Ruby.

```ruby
class Circle
	PI = 3.14
	
	attr_accessor :radius
	
	def intialize(radius)
		self.radius = radius
	end
	
	def area
		PI * radius ** 2
	end
end
```

And to use it...

```
> require 'circle'
> c = Circle.new(2)
> c.area
=> 12.56
```

If you're anything like me, you'd be tempted to do something like this when you first try out Node:

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

What happens when we try to use this module?

```
node> var circle = require('circle_object')
{ Circle: { [Function] PI: 3.14 } }
node> var c = new circle.Circle(2)
{ radius: 2 }
node> c.area()
12.56
```

Hmm. Awkward. We can get around this simple by removing the <tt>var</tt>, and dropping <tt>exports</tt> as follows:

```javascript
Circle = function (radius) {
	this.radius = radius
}

Circle.PI = 3.14

Circle.prototype = {
	area: function () {
		return Circle.PI * this.radius * this.radius
	}
}
```

And we can use it like we did with our Ruby object:

```
node> require('circle_object')
{}
node> Circle
{ [Function] PI: 3.14 }
node> var c = new Circle(3)
{ radius: 3 }
node> c.area()
28.259999999999998
```

Our Circle object was defined on the global object. So, surprise, surprise, it's globally available to us too.

However, we've seen that we don't really need to do this most of the time. We can simply write the body of our function and export the API we wish to make public.

# In Summary

Let's look at what we've learned:

* In a CommonJS module, if you want something to be available to your user, you must <code>export</code> it.
* Things that aren't exported are still available to the exported functions and values. This is why <code>area</code> can access the value of <code>PI</code>, but we couldn't. In this way, <code>PI</code> is private.
* The file defining our functions acts as a namespace. Methods and values we provide users of our module must bed exported.
* If you want to write a traditional class, define it on the global object (please see <a href="#edit_2010_04_08">edit</a>)

Try writing a module in Node. Practice what we've learned.

<a name="edit_2010_04_08"></a>
## A Note on the Final Example (edit, April 8, 2010)

In reading comments on Reddit, I wanted to make note of [masklinn's comment](http://www.reddit.com/r/javascript/comments/bnghc/understanding_nodejs_require/c0nnzqh):


{% blockquote masklinn http://www.reddit.com/r/javascript/comments/bnghc/understanding_nodejs_require/c0nnzqh %}
The piece of code at the end is downright dangerous (because it mucks up 
global objects, potentially overwriting properties in other modules), and 
there's nothing object oriented about ruby's behavior, if anything it's 
abject-oriented. Ruby's require is akin's to PHP's include or C's very own 
#include, it just dumps the required file in the local namespace and executes 
it.

Node.js's original behavior, where require actually creates and returns a 
full-fledged object is infinitely cleaner and far more object-oriented.
{% endblockquote %}

Masklinn is absolutely correct! In case it was entirely clear above, I feel 
it important to reiterate that **this is not a good way to make objects when 
using Node**. It's dangerous, and unnecessary. Try to think of the file as 
the contents of your class' body, and the exported methods as the public 
interface.
