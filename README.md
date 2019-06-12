# loopback3-tip-tricks
### Tips and tricks for loopback 3  projects

Override defaults methods of a model:

Example of a controller file: (Link for gist)[https://gist.github.com/pookdeveloper/37e249aa0195fd27b63355c515a388f8]

````javascript
'use strict';

module.exports = function (Examplemodel) {

    var app = require('../../server/server');
    var seguridad = require('../seguridad/seguridad');
    var utilidades = require('../utilidades/utilidades');

    // Override the method create for model 
    Examplemodel.once('attached', function () {
        var _create = Examplemodel.create;
        Examplemodel.create = function (filter, options, cb) {
            _create.apply(this).then((data => {
                // DO SOMETHING
                cb(null, data)
            })).catch((err => {
                // DO SOMETHING
                cb(err)
            }))
        }
    })

};
````

