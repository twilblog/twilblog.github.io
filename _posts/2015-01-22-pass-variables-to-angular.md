---
layout: post
title:  "Waiting for an API before starting an AngularJS app"
date:   2016-01-22 11:00:00:00
author: shaun
categories: AngularJS 
---
# Background

I have an Angular app that uses data from an API endpoint for various things. Let's say that the API endpoint is `/user-info`, and it returns data that looks like this:

{% highlight json %}
{
	"username": "hermione",
	"products": ["productOne", "productTwo"],
	"lastUpdated": ""2016-01-22T11:03:29.948Z""
}
{% endhighlight %}

This data is used by different services and directives in my app.

# The problem

Ordinarily, I'd retrieve the data with something like this:

{% highlight javascript %}
$http.get('/user-info').then(
	function(success) {
		// Do something with the data.
		myService.username = success.data.username;
	}
);
{% endhighlight %}

This is all well and good when the data is only required in one place, but what if `myServiceOne` and `myDirectiveTwo` both need data from this endpoint, and they're used on the same page? If you followed the above `$http.get` logic in both places, you'll end up sending the same request twice, doubling the network load.

# A possible solution?

I thought I could easily solve this by making a simple cache. To do this, I refactored my two calls to `$http.get` into a service that checks the cache before making an HTTP request.

For brevity, I've omitted error handling. In real code I handle errors, promise.

{% highlight javascript %}
.service('UserInfo', function($http, $q) {
	return {
		cache: null,
		get: function() {
			// Avoid scoping issues!
			var self = this;

			var deferred = $q.defer();
			// Check the cache first.
			if (self.cache) {
				deferred.resolve(self.cache);
			}
			else {
				$http.get('/user-info').then(
					function(success) {
						// Store the data in the cache.
						self.cache = success.data;
						deferred.resolve(self.cache);
					}
				);
			}
			return deferred.promise;
		}
	};
});
{% endhighlight %}

This solution looks pretty good, but when I used it, it didn't solve the issue. I was still seeing multiple requests being made to `/user-info` and I couldn't figure out why. Then I realised that it's a race condition of sorts. When the app loads, UserInfo.get` is run twice in quick succession (once by the directive that uses it, once by the service), which means that the API hasn't had a chance to respond yet, and the data hasn't been cached.

# The actual solution

I decided that the best way for me to tackle this issue was for the data to be available when the app starts. As far as I can tell, Angular doesn't have a way to resolve a promise before starting an app. But with some creativity you can get around this.

The beginning of my `index.html` looked like this:

{% highlight html %}
<html ng-app="myAngularApp">
{% endhighlight %}

I removed the `ng-app` attribute to stop Angular automatically starting the app on load. Then, in my `app.js` file (the one that has the root of my Angular module, I added a self executing function):

{% highlight javascript %}
// Angular module definition.
angular.module('myAngularApp', [

	// Stuff to pass into the app when it starts.
	'ngRoute',
	'myAngularApp.controllers',
	'myAngularApp.services',
	'myAngularApp.directives',
	'myAngularApp.filters',

	// Defined in the function below.
	'userInfo'
]);

// Newly added self executing function.
(function() {
	
	// Get Angular's $http module.
	var initInjector = angular.injector(['ng']);
	var $http = initInjector.get('$http');

	// Get user info.
	$http.get('/user-info').then(
		function(success) {

			// Define a 'userInfo' module.
			angular.module('userInfo', []).constant('info', success.data);

			// Start myAngularApp manually.
			angular.element(document).ready(function() {
				angular.bootstrap(document, ['myAngularApp']);
			});
		});
})();
{% endhighlight %}

As the above code shows, the function retrieves Angular's `$http` module, uses it to get the required info, makes it into a module, then manually starts the app, passing in the module.

Now, when the app starts, the cached response from `/user-info` can easily be injected. For example, here's how a controller might use it:

{% highlight javascript %}
.controller('MyController', function($scope, userInfo) {
	$scope.userInfo = userInfo;
});
{% endhighlight %}

What other ways have you found to solve this problem? Let us know <a href="https://twitter.com/twil_blog" target="_blank">on Twitter</a>.


