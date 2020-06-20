# osprey

This README describes how to quickly create a REST API and host it locally requiring only a RAML 1.0
specification. The response can ONLY be a static example. This however, can be very helpful for local
development where an external JSON REST API will be called actually in Production. The Osprey Mock
Service can be run locally on your OS as a service listener. It could also be run as a Docker container
that could be started and stopped using Docker for your local development.

The Osprey Mock Service is maintained as open source here:

[https://github.com/mulesoft-labs/osprey-mock-service](https://github.com/mulesoft-labs/osprey-mock-service)

To experiment and run this Osprey implementation example, first clone this software repository or fork it if
you have suggestions for improvement.

    git clone https://github.com/dlwhitehurst/osprey.git

## Assumptions

This README assumes that you are familiar with REST API development, RAML 1.0, NodeJS, Node Package Manager (NPM), 
and basic Javascript. If not, here are a few links to some general education:

 - [https://restfulapi.net/](https://restfulapi.net/)
 - [https://raml.org/developers/whats-new-raml-10](https://raml.org/developers/whats-new-raml-10)
 - [https://nodejs.org/en/](https://nodejs.org/en/)
 - [https://www.npmjs.com/](https://www.npmjs.com/)
 - [https://developer.mozilla.org/en-US/docs/Web/JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)


## Prerequisites

Before you can run the Osprey Mock Service you must use the Node Package Manager (NPM) and either install
the `osprey-mock-service` globally (quickest) or locally within your project directory. Please note that a local 
installation requires the use of an application Javascript module (actual source code). If you install 
globally, you can implement your own mocks anywhere using just a RAML specification.

### Global Install

    npm install -g osprey-mock-service

### Project Install

    cd <project-root>
    npm install osprey-mock-service --save

Once the `osprey-mock-service` is installed, you can run and host the service. 

## Run the Mock Service

If the `osprey-mock-service` was installed globally, you can run a mocking service with just a RAML API 
specification. The filename will be sourced into the service within the shell command that is issued.

    osprey-mock-service -f api.raml -p 3000 --cors

As long as `api.raml` is available to the shell process, `osprey-mock-service` will source the file and
provide the mock service using the RAML as mocking service's API specification. Please note that if you
installed the `osprey-mock-service` at your project root, you will need to create a simple Javascript
module to parse the RAML specification and activate the mocking service.

In this case, create a file called `app.js` and paste this into it and save the file.

    var mockService = require('osprey-mock-service')
    var express =     require('express')
    var parser =      require('raml-1-parser')
    var path =        require('path')
    var osprey =      require('osprey')

    var app = express()

    parser.loadRAML(path.join(__dirname, 'api.raml'), { rejectOnErrors: true })
      .then(function (ramlApi) {
         var raml = ramlApi.expand(true).toJSON({ serializeMetadata: false })
         app.use( osprey.server(raml) )
         app.use( mockService(raml) )
         app.listen(3000)
         console.log('Express server running on port 3000');
       }) 

Once you've created the file, you can run the new Javascript application like so:

    node app.js

Also, note that using the global service is easier and also provides more console output.

## Docker 

This mocking service would be very convenient if made into a Docker container and used a Docker volume to 
source the RAML 1.0 specification. *Stay tuned here ... this may be coming. I don't think the `osprey-mock-service`
currently utilizes or is driven by the entire RAML 1.0 specification. So a reference implementation has not
been located that I am aware of.*

## License

Apache License 2.0 
