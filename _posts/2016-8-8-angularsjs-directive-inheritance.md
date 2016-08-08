---
layout: post
comments: true
title:  "Directive Inheritance"
date:   2016-8-8
---

이 글은 [Mastering AngularJS Directives](http://code.tutsplus.com/tutorials/mastering-angularjs-directives--cms-22511) 의 내용 중 Directive Inheritance 부분을 정리한 글이다.

아래는 위 글에서 사용된 sample 소스를 바탕으로 구현한 full source 이다.

```html
<!DOCTYPE HTML>
<html lang="ko">
    <head>
        <meta charset="utf-8"/>
        <meta content="width=device-width,initial-scale=1.0,user-scalable=no" name="viewport"/>
        <title>
            Directive
        </title>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.5.7/angular.js" type="text/javascript">
        </script>
    </head>
    <body ng-app="app">
        <a><mouse-clicked ebook>Game of thrones (click me)</mouse-clicked></a><br/>
        <a><mouse-clicked magazine>PC World (click me)</mouse-clicked></a>
    </body>
    <script>
        angular.module('app', [])
        .controller('MouseClickedController', function($element) {
            var mouseClickedController = this;

            mouseClickedController.bookType = null;

            mouseClickedController.setBookType = function(type) {
                mouseClickedController.bookType = type;
            };

            $element.bind("click", function() {
                alert("book type is " + mouseClickedController.bookType);
            })
        })
        .directive('mouseClicked', function() {
            return {
                restrict: 'E',
                scope: {},
                controller: 'MouseClickedController as mouseCliked'
            }
        })
        .directive('ebook', function() {
            return {
                require: 'mouseClicked',
                link: function(scope, element, attrs, mouseClickedController) {
                    mouseClickedController.setBookType('EBOOK');
                }
            }
        })
        .directive('magazine', function() {
            return {
                require: 'mouseClicked',
                link: function(scope, element, attrs, mouseClickedController) {
                    mouseClickedController.setBookType('MAGAZINE');
                }
            }
        })
    </script>
</html>
```

`mouseClicked` directive를 생성하고 `ebook`과 `magazine` directive에서는 `require`를 이용하여 `mouseClicked`를 상속 받고 있다. 이 때 `mouseClicked` directive에서 정의한 controller가 link 함수의 4번째 인자로 넘어간다.

`mouseClicked` derective 정의 시 controller를 `MouseClickedController as mouseCliked` 이와 같이 정의 하고 있는데 이는 MouseClickedController를 mouseCliked라는 별칭으로 사용하겠다는 뜻이다.
만약 template에서 사용한다면 아래와 같이 사용할 수 있다.

```html
<body ng-app="app">
    <user name="홍길동"></user>
    <script>
        angular.module('app', [])
        .controller('UserController', function() {
            var userController = this;
            userController.name = '박철수'
        })
        .directive('user', function(){
            return {
                scope: {
                    name: '@'
                },
                controller: 'UserController as ctrl',
                // controllerAs: 'ctrl',
                template: '<div>{{'{{ctrl.name'}}}}</div>',
            };
        });
    </script>
</body>
```

위 소스는 브라우저 `박철수`를 출력한다.

```html
박철수
```