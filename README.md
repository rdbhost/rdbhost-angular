rdbhost-angular
===============

rdbhost-angular module, providing Rdbhost versions of $resource and $http


Include the module with a script tag:

    <script src="js/rdbhost-angular.js">
    

Inject the module into your app module like:

    var app = angular.module('MyApp', ['rdbhost'])
    

The module includes a $http alternative, rdbHttp, used like:


    app.controller('myCtl', ['rdbHttp', function(rdbHttp) {
    
    }


The rdbResource service is an alternative to $resource:

    app.factory('myResource', ['rdbResource', function(rdbResource) {
    
      
    }
    
    
Usage is very similar to $resource, in that the action parameter provides methods
for the class object, and non-get methods are copied to the instance objects 
retreived from server.  rdbResource, in fact, wraps $resource and relies on same.

It differs from $resource in a few ways, noted here:

1. The only required parameter is 'action', as the action elements carry the SQL queries.
2. If you use transformResponse functions, they must return JavaScript objects, not serialized.  
rdbResource will serialize for you.  The 'params' attribute of the action element may contain
'args' list or 'namedParams' object, and must include a 'q' attribute with the SQL query.
