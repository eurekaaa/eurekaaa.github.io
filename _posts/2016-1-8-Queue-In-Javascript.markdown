---
layout: post
title:  "Queue In Javascript"
date:   2016-1-8 19:02:00 +0900
categories: jekyll update
---

Queue

{% highlight javascript %}

function Queue() {
	var items = [];

	this.enqueue = function(element) {
		items.push(element);
	};

	this.dequeue = function() {
		return items.shift();
	};

	this.front = function() {
		return items[0];
	};

	this.clear = function() {
		items = [];
	};

	this.size = function() {
		return items.length;
	};

	this.isEmpty = function() {
		return items.length == 0;
	}

	this.print = function() {
		console.log(item.toString());
	};
}

console.log('____________________ Queue Test');
var queue = new Queue();
console.log(queue.size());
queue.enqueue(1);
queue.enqueue(2);
queue.enqueue(3);
console.log(queue.size());
console.log(queue.front());
console.log(queue.dequeue());

{% endhighlight %}


