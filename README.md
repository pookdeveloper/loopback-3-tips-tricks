# loopback-3-tips-tricks
## Tips, tricks and common problems solutions for Loopback 3 projects

* Override defaults methods of a model:

Example of a controller file: [Link for gist](https://gist.github.com/pookdeveloper/37e249aa0195fd27b63355c515a388f8)

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

*  Validating model data only in create
````javascript
// Use the method this.isNewRecord() to check that
Model.validate('end_date', fecha_fin, { message: 'The end_date can not be less than the start_date' });
    function end_date(err) {
        if (this.isNewRecord() && this.end_date && this.end_date < this.start_date) {
            err();
        }
};
````


*  Use await in datasource connector execute (Two options)
> One option
````
// usage
const result = await executeAsync(dataSource, sqlStatement, params, options);

// impletation of function to wrap it
function executeAsync(dataSource, sqlStatement, params, options) {
  return new Promise((resolve, reject) => {
    dataSource.connector.execute(sqlStatement, params, options, (err, result) => {
      if (err) reject(err);
      else resolve(result);
    });
  });
}
````

> Two option [documentation promisify](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_promisify_original)
````
const { promisify } = require('util')
await promisify(YOUR_MODEL.connection.query).bind(YOUR_MODEL.connection)(sql_stmt, params)
````
