---
layout: post
comments: true
title:  "ngModel.NgModelController에 대한 이해"
date:   2016-8-2
---

directive, controller, ngModel, NgModelController에 대한 설명은 아래 링크로 대신한다.<br>
아래 링크에서 "What is a directive? What is a controller?" 항목을 참고하면 된다.<br>
[Using NgModelController with Custom Directives](https://www.nadeau.tv/using-ngmodelcontroller-with-custom-directives/)

이 post의 내용은 위 article에 대한 부연 설명이다.

Custom Directive를 구현할 때 ngModel의 NgModelController를 통해 view영역의 데이터를 조작할 수 있다.

위 링크의 내용과 소스가 이해하는데 많은 도움이 되기 때문에 그대로 가져다 이해한 부분을 좀더 남겨 놓는다.


```html
<div ng-app="HelloApp" ng-controller="HelloController">
    <h1>Directive</h1>
    <time-duration ng-model="email_notify_pref"></time-duration>
    <h1>Sample bound input</h1>
    <input ng-model="email_notify_pref"></input>
</div>
```

```javascript
angular.module('HelloApp', []);

function TimeDurationDirective() {
  var tpl = "<div> \
     <input type='text' ng-model='num' size='80' /> \
     <select ng-model='unit'> \
         <option value='seconds'>Seconds</option> \
         <option value='minutes'>Minutes</option> \
         <option value='hours'>Hours</option> \
         <option value='days'>Days</option> \
     </select> \
 </div>";

  return {
      restrict: 'E',
      template: tpl,
      require: 'ngModel',
      replace: true,
      link: function(scope, iElement, iAttrs, ngModelCtrl) {
          // Units of time
          var multiplierMap = {seconds: 1, minutes: 60, hours: 3600, days: 86400};
          var multiplierTypes = ['seconds', 'minutes', 'hours', 'days']

          ngModelCtrl.$formatters.push(function(modelValue) {
              var unit = 'minutes', num = 0, i, unitName;

              modelValue = parseInt(modelValue || 0);

              // Figure out the largest unit of time the model value
              // fits into. For example, 3600 is 1 hour, but 1800 is 30 minutes.
              for (i = multiplierTypes.length-1; i >= 0; i--) {
                  unitName = multiplierTypes[i];
                  if (modelValue % multiplierMap[unitName] === 0) {
                      unit = unitName;
                      break;
                  }
              }

              if (modelValue) {
                  num = modelValue / multiplierMap[unit]
              }

              return {
                  unit: unit,
                  num:  num
              };
          });

          ngModelCtrl.$parsers.push(function(viewValue) {
              var unit = viewValue.unit, num = viewValue.num, multiplier;
              multiplier = multiplierMap[unit];
              return num * multiplier;
          });

          scope.$watch('unit + num', function() {
              ngModelCtrl.$setViewValue({ unit: scope.unit, num: scope.num });
          });

          ngModelCtrl.$render = function() {
              scope.unit = ngModelCtrl.$viewValue.unit;
              scope.num  = ngModelCtrl.$viewValue.num;
          };
      }
  };
};

angular.module('HelloApp').directive('timeDuration', TimeDurationDirective);

angular.module('HelloApp').controller('HelloController', function($scope) {
	$scope.email_notify_pref = 3600;
});
```
1.
아래와 같이 directive를 선언하면 `$formatters pipe function`의 modelValue parameter에 `HelloController`의 scope에 정의한 email_notify_pref의 값인 3600이 전달되며 `$formatters pipe function`이 실행된다.

```html
<time-duration ng-model="email_notify_pref"></time-duration>
```

여기서 `pipe function`이라함은 이전 결과를 전달 받는 function을 의미한다.

`$formatters pipe function`이 실행되고 `$viewValue`를 리턴한다. 여기서의 `$viewVaule`는 실제 UI에 노출될 값이다.
리턴값은 아래와 같은데 리턴되는 Object의 key는 HTML의 `input`, `select`의 ng-model의 값과 동일하다. `ng-model="unit"`, `ng-model="num"`

```javascript
return {
	unit: unit,
	num:  num
};
```
2.
`$formatters`가 실행 완료된 후에 `$render` 가 실행된다. `$render`에서는 scope의 `unit`, `num` 변수의 값을 변경된 viewValue의 `unit`, `num`으로 변경한다.

3.
`unit`과 `num`은 `$watch`에 의해 감시되고 있었기 때문에 값이 변경되면 `scope.$watch`가 실행된다.

4.
`scope.$watch`에서는 `$setViewValue`에 scope의 값을 담는다.

5.
`$setViewValue`를 호출하면 그 다음에 `$parsers pipe function`이 실행된다.
이 때 변경된 viewValue의 값이 `$parsers`의 인자값으로 전달된다.

6.
`$parsers`는 viewValue를 받아 modelValue를 생성한다.

정리하면, 아래와 같은 순서로 진행이된다.

* modelValue를 변경한 경우(여기서는 `email_notify_pref` 값이 변경된 경우이다.)
  * modelValue 변경 -> $formatters -> $render -> ($watch) -> $setViewValue -> $parsers -> modelValue
* UI 값이 변경된 경우(여기서는 input(unit, num)의 값이 변경된 경우이다.)
  * $watch -> $setViewValue -> $parsers -> modelValue


