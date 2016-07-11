---
layout: post
comments: true
title:  "Dictionary In Javascript"
date:   2016-1-7 23:37:00 +0900
categories: jekyll update
---

Dictionary

{% highlight javascript %}

function Dictionary() {
	var items = {};

	this.has = function(key) {
		return key in items;
	};

	this.set = function(key, value) {
		items[key] = value;
	};

	this.remove = function(key) {
		if (this.has(key)) {
			delete items[key];
			return true;
		}
		return false;
	};

	this.get = function(key) {
		return this.has(key) ? items[key] : undefined;
	};

	this.values = function() {
		var values = [];
		for (var k in items) {
			if (this.has(k)) {
				values.push(items[k]);
			}
		}
		return values;
	};

	this.clear = function() {
		items = {};
	};

	this.size = function() {
		return Object.keys(items).length;
	};

	this.keys = function() {
		var keys = []
		for (var key in items) {
			keys.push(key);
		}
		return keys;
	};
}

var dictionary = new Dictionary();
dictionary.set('AAA', '010-1111-1111');
dictionary.set('BBB', '010-2222-2222');
dictionary.set('CCC', '010-3333-3333');
console.log(dictionary.has('AAA'));
console.log(dictionary.size());
console.log(dictionary.keys());
console.log(dictionary.values());
console.log(dictionary.get('CCC'));
dictionary.remove('CCC');
console.log(dictionary.size());
console.log(dictionary.keys());
console.log(dictionary.values());
dictionary.clear();
console.log(dictionary.size());
dictionary.set('AAA', '010-1111-1111');
dictionary.set('BBB', '010-2222-2222');
console.log(dictionary.size());
console.log(dictionary.keys());
console.log(dictionary.values());

{% endhighlight %}


