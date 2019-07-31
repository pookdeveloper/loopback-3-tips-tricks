# loopback-3-tips-tricks
## Tips, tricks and common problems solutions for Loopback 3 projects

* Validate model with isValid without in upsert:

````javascript
'use strict';

module.exports = function (MyModel) {

    var app = require('../../server/server');


    // Disable method update
    MyModel.disableRemoteMethodByName("replaceById", true);

    MyModel.validateAsync('cementerios', cementerios_validacion, {}); // { message: 'No se ha encontrado el patio' }
    async function cementerios_validacion(err, done) {
        //
        // Use your custom variable for explicit validation is (validation_manual) to check if is called explicit 
        // if(this.validation_manual) .....
        /*
        YOUR CODE...
        */
    }

    MyModel.modificar = function (req, res, options, id, data, cb) {
        // Use isvalid
        const mymodel_data = new app.models.MyModel(data);
        mymodel_data.validation_manual = true;

        myModel.isValid(function (valid) {
            if (!valid) {
                mymodel_data.errors // hash of errors {attr: [errmessage, errmessage, ...], attr: ...}
            }
        })
        /*
        YOUR CODE...
        */
    };

    // remoteMethod
    MyModel.remoteMethod(
        'modificar', {
            accepts: [
                { arg: 'req', type: 'object', 'http': { source: 'req' } },
                { arg: 'res', type: 'object', 'http': { source: 'res' } },
                { arg: "options", type: "object", http: "optionsFromRequest" },
                { arg: 'id', type: 'number', required: true },
                { arg: 'data', type: "object", 'http': { source: 'body' } }
            ],
            http: {
                path: '/modificar/:id',
                verb: 'put'
            },
            returns: {
                arg: 'cementerio',
                type: 'object', root: true
            }
        }
    );
};
````


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
await promisify(mysqlDs.connector.execute).bind(mysqlDs.connector)(sql_stmt, params)
````
