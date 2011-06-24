# Gearman-Worker

**Gearman-Worker** is a Node.JS client/worker module for Gearman.

## Usage

### Worker

    var Gearman = require("./gearman");

    worker= new Gearman();
    worker.addServer("localhost", 7003);
    worker.addFunction("upper", function(payload, job){
        var response =  payload.toString("utf-8").toUpperCase();
        job.complete(response);
    });
    
### Client

    var Gearman = require("./gearman");

    client = new Gearman();
    client.addServer("localhost", 7003);

    var job = client.submitJob("upper", "hello world!", {encoding:'utf-8'});

    job.on("complete", function(handle, response){
        console.log(response); // HELLO WORLD!
        client.end();
    });

## API

### Require Gearman library

    var Gearman = require("./gearman");

### Create a new Gearman worker/client

    var gearman = new Gearman();
    
### Add a server

    gearman.addServer([host][, port])

Where

  * **host** is the hostname of the Gearman server (defaults to localhost);
  * **port** is the Gearman port (default is 4730)

Example

    worker.addServer(); // use default values
    worker.addServer("gearman.lan", 7003);
    
### Register for exceptions

Exceptions are not sent to the client by default. To turn these on use the following command. 

    client.getExceptions([callback])
    
Where

  * **callback** is an optional callback function with params *error* if an error occured and *success* which is true if the command succeeded

Example

    client = new Gearman();
    client.addServer(); // use default values
    worker.getExceptions();
    
    job = client.submitJob("reverse", "Hello world!");
    
    job.on("error", function(exception){
        console.log(exception);
    });
    
### Submit a job

    client.submitJob(func, payload[, options])
    
Where

  * **func** is the function name
  * **payload** is either a String or a Buffer value
  * **options** is an optional options object (see below)
  
Possible option values

  * **encoding** - indicates the encoding for the job response (default is Buffer). Can be "utf-8", "ascii", "base64" or "buffer"
  * **background** - if set to true, detach the job from the client (complete and error events will not be sent to the client)
  * **priority** - indicates the priority of the job. Possible values "low", "normal" (default) and "high"
  
Returns a Gearman Job object with the following events

  * **created** - when the function is queued by the server (params: handle value) 
  * **complete** - when the function returns (params: response data in encoding specified by the options value)
  * **fail** - when the function fails (params: none)
  * **error** - when an exception is thrown (params: error string)
  * **warning** - when a warning is sent (params: warning string)
  * **status** - when the status of a long running function is updated (params: numerator numbber, denominator number)
  * **data** - when partial data is available (params: response data in encoding specified by the options value)
  
Example

    client = new Gearman();
    client.addServer(); // use default values
    worker.getExceptions();
    
    job = client.submitJob("reverse", "Hello world!", {encoding:"utf-8"});
    
    job.on("complete", function(response){
        console.log(response); // !dlrow olleH
    });
    
    job.on("fail", function(){
        console.log("Job failed :S");
    });
    
### Create a worker function

