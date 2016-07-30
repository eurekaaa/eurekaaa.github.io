---
layout: post
comments: true
title:  "AngularJS 모듈간 데이터 전달 방법"
date:   2016-7-13
---

<p>AngularJS에서 모듈 사이에 데이터를 전달 하는 방법에 대해 알아본다.</p>

쇼핑몰에서 결제 시스템에 사용자 정보를 넘기는 것을 예로 작성해야겠다.<br>
우선 아래와 같이 `mall` 이라는 AngularJS Application을 작성한다.

application.js

```javascript
var mainApplicationModuleName = 'mall';

var mainApp = angular.module(mainApplicationModuleName, ['payment']);
```

그리고 `결제시스템` 역할을 하는 `payment` 모듈을 생성한다.<br>

payment.js

```javascript
var paymentModule = angular.module('payment', []);
paymentModule.controller('PaymentController', ['$scope', function($scope){
	$scope.getUserInfo = function() {
		return '';
	};
}]);
```

마지막으로 아래는 초간단 html 페이지 이다.<br>

index.html

```html
<!DOCTYPE HTML>
<html lang="ko">
<head>
	<meta charset="UTF-8" />
	<meta name="viewport" content="width=device-width,initial-scale=1.0,user-scalable=no" />
	<title>Service</title>
	<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.5.7/angular.js" type="text/javascript"></script>
</head>
<body ng-app="mall">

<div ng-controller="PaymentController">
	사용자 이름: {{getUserInfo().name}}<br>
	사용자 ID: {{getUserInfo().id}}
</div>

<script src="application.js" type="text/javascript"></script>
<script src="payment.js" type="text/javascript"></script>
<script src="user.js" type="text/javascript"></script>
</body>
</html>
```
여기까지 (초간단)결제시스템 모듈이 준비되었다.
이제 사용자정보를 관리하는 user 모듈을 만들어 결제시스템에서 사용자 정보를 가져오도록 하자.<br>

user.js

```javascript
var userModule = angular.module('user', []);
userModule.factory('UserInfo', [function(){
	this.info = {name: '홍길동', id: 'hong77'}

	return {
		info: this.info
	};
}]);
```

user 모듈을 mall application에 의존성 주입을 하고<br>

application.js

```javascript
var mainApp = angular.module(mainApplicationModuleName, ['payment', 'user']);
```

index.html에 user.js를 import 한다.

index.html

```html
<script src="application.js" type="text/javascript"></script>
<script src="payment.js" type="text/javascript"></script>
<script src="user.js" type="text/javascript"></script>
```

user 모듈의 사용자 정보를 payment 모듈에 보내기 위해서는 payment 모듈의 PaymentController에 UserInfo factory를 주입해주어야한다.<br>

payment.js

```javascript
var paymentModule = angular.module('payment', []);
paymentModule.controller('PaymentController', ['$scope', 'UserInfo', function($scope, UserInfo){
	$scope.getUserInfo = function() {
		return UserInfo.info;
	};
}]);
```

