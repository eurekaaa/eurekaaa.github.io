---
layout: post
comments: true
title:  "ngModel.NgModelController"
date:   2016-7-30
---

# ngModel.NgModelController

directive, controller, ngModel, NgModelController에 대한 설명은 아래 링크로 대신한다.<br>
아래 링크에서 "What is a directive? What is a controller?" 항목을 참고하면 된다.<br>
[Using NgModelController with Custom Directives](https://www.nadeau.tv/using-ngmodelcontroller-with-custom-directives/)

이 post의 내용은 위 article의 추가 내용이다.

Custom Directive를 구현할 때 ngModel의 NgModelController를 통해 view영역의 데이터를 조작할 수 있다.


<code class="html">
	<div ng-app="HelloApp" ng-controller="HelloController">
        <h1>Directive</h1>
        <time-duration ng-model="email_notify_pref"></time-duration>
        <h1>Sample bound input</h1>
        <input ng-model="email_notify_pref"></input>
    </div>
</code>
