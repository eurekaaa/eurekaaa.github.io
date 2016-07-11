---
layout: post
title:  "AngularJS의 Scope 이해하기"
date:   2016-6-8
---

<p><b>$scope 객체는 뷰에게 도메인 모델을 제공한다.</b></p>

$scope의 생성은 DOM 트리에서 $scope를 생성하는 디렉티브를 만날 때마다 Scope 클래스의 인스턴스를 새로 생성한다.<br>
그 중 하나가 ng-controller 디렉티브이고 controller가 생성될 때 Scope.$new() 메소드를 통해 새로운 스코프가 생성된다.<br>

<p><b>ng-repeat 디렉티브는 반복되는 요소마다 $scope가 생성된다.</b></p>

아래 코드를 보자.
{% highlight javascript %}
var myApp = angular.module('myApp', []);
myApp.controller('ActorController', ['$scope', function($scope) {
	$scope.people = [
		{firstName: 'James', lastName: 'Dean'},
		{firstName: 'Courtney', lastName: 'Eaton'}
	];
}]);
{% endhighlight %}
{% highlight html %}
<body ng-app='myApp'>
	<ul ng-controller="ActorController">
		<li ng-repeat="person in people">
			I'm {{"{{person.firstName"}}}} {{"{{person.lastName"}}}}.<br>
		</li>
	</ul>
</body>
{% endhighlight %}

ng-repeat directive는 반복되는 요소마다 Scope을 생성한다.<br>
만약 Scope이 하나라면 반복되는 요소에 person(model)의 값을 덮어써야하는데 그렇게되면 각 요소별로 model을 유지하기가 어렵기 때문에 AngularJS에서는 Scope을 각각 반복되는 요소마다 생성한다.
<img src='{{site.url}}/assets/imgs/angularjs_scope_0.jpg'>

<h3>Scope 계층 구조와 상속</h3>
기본적으로 AngularJS의 Scope은 Javascript의 prototype 계층 구조 및 상속 방식을 그대로 따른다.<br>
ActorController 에 getFullName() 를 추가하면 반복되는 요소의 Scope에서 접근이 가능하다.
{% highlight javascript %}
var myApp = angular.module('myApp', []);
myApp.controller('ActorController', ['$scope', function($scope) {
	$scope.lastName = 'Hong';
	$scope.people = [
		{firstName: 'James', lastName: 'Dean'},
		{firstName: 'Courtney', lastName: 'Eaton'}
	];

	$scope.getFullName = function(firstName, lastName) {
		return [firstName, lastName].join(' ');
	}
}]);
{% endhighlight %}
{% highlight html %}
<body ng-app='myApp'>
	<ul ng-controller="ActorController">
		<li ng-repeat="person in people">
			I'm {{"{{getFullName(person.firstName, person.lastName)"}}}}.
		</li>
	</ul>
</body>
{% endhighlight %}
