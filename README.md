# Directive Controllers

## Overview

Whilst using `scope` is awesome, we can't really do much with it other than just display some data that is passed through to us. What if we need to manipulate this data? Well, this is where our controllers come in.

## Objectives

- Describe Directive Controllers
- Create a Controller using "controller"
- Import an existing Controller to a Directive

## Directive Controllers

We can create controllers for our directives - awesome! We'd generally use these for functions to change data or retrieve data from a service (we might have a list of contacts), or manipulate the data given us (we might want to verify an email address or phone number for a contact).

Our controllers, much like the controllers we've created before, allow us to directly access `$scope` (the values passed through to us from attributes), as well as all the services that we have created, as well as built-in ones too, such as `$timeout`.

## But, how?

You've probably guessed how we can do this in your head. We attach a property named `controller` to our directives object!

This, for now, will take a function that will become our directive's controller:

```html
<twitter-card handle="billgates"></twitter-card>
```

(this will be the code to initiate all of our examples)

```js
function TwitterCard() {
	return {
		template: [
			'<div class="twitter">',
				'<a href="https://twitter.com/{{ handle }}">Follow @{{ handle }} on Twitter!</a>',
			'</div>'
		].join(''),
		scope: {
            handle: '@'
        },
        controller: function ($scope) {
            // $scope.handle = 'billgates'
        },
		restrict: 'E'
	};
}

angular
	.module('app')
	.directive('twitterCard', TwitterCard);
```

Now we can use our controller exactly like what we've done before. We can manipulate data, or call a `$timeout`. For example, we could do this:

```js
function TwitterCard() {
	return {
		template: [
			'<div class="twitter">',
				'<a href="https://twitter.com/{{ twitter }}">Follow @{{ twitter }} on Twitter!</a>',
			'</div>'
		].join(''),
		scope: {
            handle: '@'
        },
        controller: function ($scope, $timeout) {
            $timeout(function () {
                $scope.handle = 'angularjs'
            }, 5000);
        },
		restrict: 'E'
	};
}

angular
	.module('app')
	.directive('twitterCard', TwitterCard);
```

This will set our Twitter handle to "angularjs" after 5 seconds.

## controllerAs

As you've learned already, the best practise is in fact to use the `controllerAs` syntax. But we're passing a function through as our controller, so how are we going to tell Angular that we want to use `controllerAs`? You guessed it - the `controllerAs` property.

Much like our `ng-controller`, where we use `SomeController as some`, we use `controllerAs` and put it's value as `some` (or whatever you want to call it).

```js
function TwitterCard() {
	return {
		template: [
			'<div class="twitter">',
				'<a href="https://twitter.com/{{ handle }}">Follow @{{ handle }} on Twitter!</a>',
				'<button ng-click="ctrl.changeHandle()">Change Handle</button>',
			'</div>'
		].join(''),
		scope: {
            handle: '@'
        },
        controller: function ($scope) {
            // $scope.handle === 'billgates'

            this.changeHandle = function () {
                $scope.handle = 'angularjs';
            };
        },
        controllerAs: 'ctrl',
		restrict: 'E'
	};
}

angular
	.module('app')
	.directive('twitterCard', TwitterCard);
```

As you can see here, we're setting our controller as `ctrl`. This means that anything we attach to `this` in the controller will be accessible via the `ctrl` object in our template - exactly like our controllers before!

You might have noticed how `handle` is still on our `$scope` object - this is because it's passed through as a scope property. We'll cover how to get that into `this` soon.

We've got a function to change our Twitter handle. This updates `$scope` - where our data is stored. As we trigger this event using `ng-click`, any changes that we do to `$scope` inside the function is automatically reflected in the view.

## Existing Controllers

Instead of a function, we can pass through a string to the `controller` property as well - much like what we do with `ng-controller`. This means we can use an existing controller as our directive's controller. It also means that we can remove the `controllerAs` property if we're using a string, as we can do `'SomeController as some'`.

```html
<twitter-card handle="billgates"></twitter-card>
```

```js
function TwitterController($scope) {
	// $scope.handle === 'billgates';
}

angular
	.module('app')
	.controller('TwitterController', TwitterController);

function TwitterCard() {
	return {
		template: [
			'<div class="twitter">',
				'<a href="https://twitter.com/{{ handle }}">Follow @{{ handle }} on Twitter!</a>',
				'<button ng-click="ctrl.changeHandle()">Change Handle</button>',
			'</div>'
		].join(''),
		scope: {
            handle: '@'
        },
        controller: 'TwitterController as ctrl',
		restrict: 'E'
	};
}

angular
	.module('app')
	.directive('twitterCard', TwitterCard);
```

Here we've got our controller initiated with our scope data, ready for us to manipulate it.

Now we can manipulate our data to our hearts content. Our controllers can also have our services (such as `$timeout` or a custom service) injected so we can utilise the power of them in our directives too!

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/angular-directive-controllers-readme'>Angular Directive Controllers</a> on Learn.co and start learning to code for free.</p>
