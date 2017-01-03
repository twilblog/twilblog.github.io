---
layout: post
title:  "Angular Scoping With ng-if"
date:   2017-01-03 12:00:00:00
author: shaun
categories: Angular JavaScript
---
# Background

When using Angular 1.x, you might have a simple form that looks like this:

{% highlight html %}
<input type="text" ng-model="someInput" />
<input type="text" ng-model="someOtherInput" ng-if="someConditionToCheck" />
<!--  Note the use of 'ng-if' on 'someOtherInput'. -->

<button ng-click="save()">Save</button>
{% endhighlight %}

{% highlight javascript %}
function save() {
  console.log($scope.someInput, $scope.someOtherInput);
}
{% endhighlight %}

However, if you ran this code and clicked 'Save', `$scope.someOtherInput`
would be undefined.

# WTF is going on?

The issue is that `ng-if` creates its own scope, so `someOtherInput` isn't
defined on your usual controller scope.

# Workaround

Fortunately the workaround is simple. Just use the [controllerAs](https://toddmotto.com/digging-into-angulars-controller-as-syntax/) syntax,
which is generally useful for various other reasons.
