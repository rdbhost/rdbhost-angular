rdbhost-angular
===============

rdbhost-angular module, providing Rdbhost versions of $resource and $http


Include the module with a script tag, loading it after angular modules, jquery.js, and jquery.rdbhost.js:


      <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.js"></script>
      <script src="http://www.rdbhost.com/js/jquery.rdbhost.js"></script>
      <script src="http://www.rdbhost.com/vendor/angular/1.2/angular.js"></script>

      <script src="http://www.rdbhost.com/js/rdbhost-angular.js">
    

Inject the module into your app module like:

    var app = angular.module('MyApp', ['rdbhost']);

    

Init the Rdbhost options with:

    window.Rdbhost.rdbHostConfig({
        userName: 'preauth',
        domain: 'www.rdbhost.com',
        accountNumber: 12
    });



The module includes an $http alternative, rdbHttp, used like:


    app.controller('myCtl', ['rdbHttp', '$scope', function(rdbHttp, $scope) {
    
            p = rdbHttp({
                url: 'https://' + window.Rdbhost.rdbHostConfig.opts.domain + '/db/' + R.role(),
                params: {
                    q: 'SELECT 2 AS two',
                    format: 'json-easy'
                },
                method: 'GET'
            });
            p.success(function(resp, status, headers, cfg) {
                $scope.v = resp.records.rows[0].two;
            });
            p.error(function(resp, status, header, cfg) {
                alert('error ' + resp);
            });
    }


Methods 'post', and 'get' are defined on the rdbHttp, behaving as their $http equivelants do.


The rdbResource service is an alternative to $resource:

    app.factory('myResource', ['rdbResource', '$rootScope', function(rdbResource, $rootScope) {
    
        return rdbResource(
            { // defaultParams
                userName: window.Rdbhost.role()
            },
            { // actions
                get: {
                    params: {
                        q: 'SELECT key, id FROM accounts WHERE key = %(key)',
                        namedParams: {
                            'key': function() { return $rootScope.favekey }
                        }

                    }
                },

                save: {
                    method: 'POST',
                    params: {
                        q: 'INSERT INTO accounts (key) VALUES(%(key)); ',
                        namedParams: {
                            'key': function() { return $rootScope.favekey }
                        }
                    }
                }

            }
        );
    }
    
    
Usage is very similar to $resource, in that the action parameter provides methods
for the class object, and non-get methods are copied to the instance objects 
retreived from server.  rdbResource, in fact, wraps $resource and relies on same.

It differs from $resource in a few ways, noted here:

1. The only required parameter is 'action', as the action elements carry the SQL queries.
2. If you use transformResponse functions, they must return JavaScript objects, not serialized. rdbResource will serialize for you.  The 'params' attribute of the action element may contain 'args' list or 'namedParams' object, and must include a 'q' attribute with the SQL query.
3. If paramDefault is not provided, or does not contain an Rdbhost userName and domain, those values will be retrieved from the window.Rdbhost.rdbHostConfig.opts hash.
