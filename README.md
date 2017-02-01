# Test typescript 2, express server and node

This is based on [Brian Love's article](http://brianflove.com/2016/11/08/typescript-2-express-node/)
but I'll be trying it on my linux machine and using `yarn`.

Below are the step by step procedures, to use this repo directly:
```
git clone https://github.com/winksaville/test-typescript-2-express-node.git
cd test-typescript-2-express-node.git
yarn install
yarn run grunt
yarn start
```
And then in a browser access `localhost:8080' something like:
```
chromium http://localhost:8080
```
##Create directory and the initial package.json
```
mkdir test-typescript-2-express-node
cd test-typescript-2-express-node
yarn init
```
I then edited package.json as Brian suggested:
```
$ cat package.json 
{
  "name": "test-typescript-2-express-node",
  "version": "0.1.0",
  "description": "Test typescript 2, express server and node js",
  "main": "index.js",
  "repository": "https://github.com/winksaville/test-typescript-2-express-node",
  "author": "Wink Saville",
  "license": "Unlicense",
  "scripts": {
    "dev": "NODE_ENV=development nodemon ./win/www",
    "grunt": "grunt",
    "start": "node ./bin/www"
  }
}
```
## Installed express
```
yarn add express

```
## Add start script, see Brian's article for more info.
```
mkdir bin
vi bin/www
$ cat bin/www
#!/usr/bin/env node
"use strict";

//module dependencies
var server = require("../dist/server");
var debug = require("debug")("express:server");
var http = require("http");

//create http server
var httpPort = normalizePort(process.env.PORT || 8080);
var app = server.Server.bootstrap().app;
app.set("port", httpPort);
var httpServer = http.createServer(app);

//listen on provided ports
httpServer.listen(httpPort);

//add error handler
httpServer.on("error", onError);

//start listening on port
httpServer.on("listening", onListening);


/**
 * Normalize a port into a number, string, or false.
 */
function normalizePort(val) {
  var port = parseInt(val, 10);

  if (isNaN(port)) {
    // named pipe
    return val;
  }

  if (port >= 0) {
    // port number
    return port;
  }

  return false;
}

/**
 * Event listener for HTTP server "error" event.
 */
function onError(error) {
  if (error.syscall !== "listen") {
    throw error;
  }

  var bind = typeof port === "string"
    ? "Pipe " + port
    : "Port " + port;

  // handle specific listen errors with friendly messages
  switch (error.code) {
    case "EACCES":
      console.error(bind + " requires elevated privileges");
      process.exit(1);
      break;
    case "EADDRINUSE":
      console.error(bind + " is already in use");
      process.exit(1);
      break;
    default:
      throw error;
  }
}

/**
 * Event listener for HTTP server "listening" event.
 */
function onListening() {
  var addr = httpServer.address();
  var bind = typeof addr === "string"
    ? "pipe " + addr
    : "port " + addr.port;
  debug("Listening on " + bind);
}
```
## Install Typescript and Grunt and grun task runners
```
yarn add typescript --dev
yarn add grunt --dev
yarn add grunt-contrib-copy grunt-ts grunt-contrib-watch --dev
```
## Add the gruntfile.js and again, see Brian's article for explanation
```
$ cat gruntfile.js
module.exports = function(grunt) {
  "use strict";

  grunt.initConfig({
    copy: {
      build: {
        files: [
          {
            expand: true,
            cwd: "./public",
            src: ["**"],
            dest: "./dist/public"
          },
          {
            expand: true,
            cwd: "./views",
            src: ["**"],
            dest: "./dist/views"
          }
        ]
      }
    },
    ts: {
      app: {
        files: [{
          src: ["src/\*\*/\*.ts", "!src/.baseDir.ts"],
          dest: "./dist"
        }],
        options: {
          module: "commonjs",
          target: "es6",
          sourceMap: false
        }
      }
    },
    watch: {
      ts: {
        files: ["src/\*\*/\*.ts"],
        tasks: ["ts"]
      },
      views: {
        files: ["views/**/*.pug"],
        tasks: ["copy"]
      }
    }
  });

  grunt.loadNpmTasks("grunt-contrib-copy");
  grunt.loadNpmTasks("grunt-contrib-watch");
  grunt.loadNpmTasks("grunt-ts");

  grunt.registerTask("default", [
    "copy",
    "ts"
  ]);

};
```
## I then ran `yarn run grunt` and as expected saw 'No files to compile':
```
$ yarn run grunt
yarn run v0.20.0
$ grunt 
Running "copy:build" (copy) task


Running "ts:app" (ts) task
No files to compile

Done.
Done in 0.73s.
```
## Install middleware:
```
yarn add body-parser cookie-parser morgan errorhandler method-override
```
And yarn said:
```
yarn add v0.20.0
[1/4] Resolving packages...
[2/4] Fetching packages...
warning fsevents@1.0.17: The platform "linux" is incompatible with this module.
info "fsevents@1.0.17" is an optional dependency and failed compatibility check. Excluding it from installation.
[3/4] Linking dependencies...
[4/4] Building fresh packages...
success Saved lockfile.
success Saved 10 new dependencies.
├─ basic-auth@1.0.4
├─ body-parser@1.16.0
├─ cookie-parser@1.4.3
├─ debug@2.3.3
├─ errorhandler@1.5.0
├─ iconv-lite@0.4.15
├─ method-override@2.3.7
├─ morgan@1.7.0
├─ on-headers@1.0.1
└─ raw-body@2.2.0
Done in 1.92s.
```
## Install @types

These added to devDependencies because types are only used durning
compiliation
```
yarn add @types/body-parser @types/cookie-parser @types/morgan @types/errorhandler @types/method-override --dev
```
And yarn said:
```
yarn add v0.20.0
[1/4] Resolving packages...
[2/4] Fetching packages...
warning fsevents@1.0.17: The platform "linux" is incompatible with this module.
info "fsevents@1.0.17" is an optional dependency and failed compatibility check. Excluding it from installation.
[3/4] Linking dependencies...
[4/4] Building fresh packages...
success Saved lockfile.
success Saved 10 new dependencies.
├─ @types/body-parser@0.0.33
├─ @types/cookie-parser@1.3.30
├─ @types/errorhandler@0.0.30
├─ @types/express-serve-static-core@4.0.40
├─ @types/express@4.0.35
├─ @types/method-override@0.0.29
├─ @types/mime@0.0.29
├─ @types/morgan@1.7.32
├─ @types/node@7.0.4
└─ @types/serve-static@1.7.31
Done in 2.30s.
```
## Create `Server` Class

As before Brian has a nice explanation of the class.
```
mkdir src
vi server.ts
```
And server.ts is
```
import * as bodyParser from "body-parser";
import * as cookieParser from "cookie-parser";
import * as express from "express";
import * as logger from "morgan";
import * as path from "path";
import errorHandler = require("errorhandler");
import methodOverride = require("method-override");

/**
 * The server.
 *
 * @class Server
 */
export class Server {

  public app: express.Application;

  /**
   * Bootstrap the application.
   *
   * @class Server
   * @method bootstrap
   * @static
   * @return {ng.auto.IInjectorService} Returns the newly created injector for this app.
   */
  public static bootstrap(): Server {
    return new Server();
  }

  /**
   * Constructor.
   *
   * @class Server
   * @constructor
   */
  constructor() {
    //create expressjs application
    this.app = express();

    //configure application
    this.config();

    //add routes
    this.routes();

    //add api
    this.api();
  }

  /**
   * Create REST API routes
   *
   * @class Server
   * @method api
   */
  public api() {
    //empty for now
  }

  /**
   * Configure application
   *
   * @class Server
   * @method config
   */
  public config() {
    //empty for now
  }

  /**
   * Create router
   *
   * @class Server
   * @method api
   */
  public routes() {
    //empty for now
  }
}
```
I then ran grunt to build what we have so far:
```
$ yarn run grunt
yarn run v0.20.0
$ grunt 
Running "copy:build" (copy) task


Running "ts:app" (ts) task
Compiling...
Using tsc v2.1.5



TypeScript compilation complete: 1.35s for 2 TypeScript files.

Done.
Done in 2.08s.
```
## Add Server.config code:
```
/**
  * Configure application
  *
  * @class Server
  * @method config
  */
public config() {
  //add static paths
  this.app.use(express.static(path.join(__dirname, "public")));

  //configure pug
  this.app.set("views", path.join(__dirname, "views"));
  this.app.set("view engine", "pug");

  //use logger middlware
  this.app.use(logger("dev"));

  //use json form parser middlware
  this.app.use(bodyParser.json());

  //use query string parser middlware
  this.app.use(bodyParser.urlencoded({
    extended: true
  }));

  //use cookie parker middleware middlware
  this.app.use(cookieParser("SECRET_GOES_HERE"));

  //use override middlware
  this.app.use(methodOverride());

  //catch 404 and forward to error handler
  this.app.use(function(err: any, req: express.Request, res: express.Response, next: express.NextFunction) {
      err.status = 404;
      next(err);
  });

  //error handling
  this.app.use(errorHandler());
}
```
## Add pug and create public and views directories
```
yarn add pug
mkdir public
mkdir views
```
And here is what the directory structure is now:
```
$ tree -I node_modules 
.
├── bin
│   └── www
├── dist
│   └── server.js
├── gruntfile.js
├── package.json
├── public
├── README.md
├── src
│   └── server.ts
├── UNLICENSE
├── views
└── yarn.lock

5 directories, 8 files
```
## We can now compile the server
```
$ yarn run grunt
yarn run v0.20.0
$ grunt 
Running "copy:build" (copy) task


Running "ts:app" (ts) task
Compiling...
Cleared fast compile cache for target: app
Using tsc v2.1.5



TypeScript compilation complete: 1.45s for 2 TypeScript files.

Done.
Done in 2.17s.
```
Run the server:
```
$ yarn start
yarn start v0.20.0
$ node ./bin/www 
```
And if we access a client the server from a chromium browser we
get the result `Cannot GET /`:
```
$ chromium http://localhost:8080
```
The server outputs a 404:
```
GET / 404 0.718 ms - 13
```

## To solve the 404 we'll create routes

./src/routes/route.ts and ./src/routes/indes.ts:
```
$ cat src/routes/route.ts 
import { NextFunction, Request, Response } from "express";

/**
 * Constructor
 *
 * @class BaseRoute
 */
export class BaseRoute {

  protected title: string;

  private scripts: string[];

  /**
   * Constructor
   *
   * @class BaseRoute
   * @constructor
   */
  constructor() {
    //initialize variables
    this.title = "Tour of Heros";
    this.scripts = [];
  }

  /**
   * Add a JS external file to the request.
   *
   * @class BaseRoute
   * @method addScript
   * @param src {string} The src to the external JS file.
   * @return {BaseRoute} Self for chaining
   */
  public addScript(src: string): BaseRoute {
    this.scripts.push(src);
    return this;
  }

  /**
   * Render a page.
   *
   * @class BaseRoute
   * @method render
   * @param req {Request} The request object.
   * @param res {Response} The response object.
   * @param view {String} The view to render.
   * @param options {Object} Additional options to append to the view's local scope.
   * @return void
   */
  public render(req: Request, res: Response, view: string, options?: Object) {
    //add constants
    res.locals.BASE_URL = "/";

    //add scripts
    res.locals.scripts = this.scripts;

    //add title
    res.locals.title = this.title;

    //render view
    res.render(view, options);
  }
}
```
```
$ cat src/routes/index.ts 
import { NextFunction, Request, Response, Router } from "express";
import { BaseRoute } from "./route";


/**
 * / route
 *
 * @class User
 */
export class IndexRoute extends BaseRoute {

  /**
   * Create the routes.
   *
   * @class IndexRoute
   * @method create
   * @static
   */
  public static create(router: Router) {
    //log
    console.log("[IndexRoute::create] Creating index route.");

    //add home page route
    router.get("/", (req: Request, res: Response, next: NextFunction) => {
      new IndexRoute().index(req, res, next);
    });
  }

  /**
   * Constructor
   *
   * @class IndexRoute
   * @constructor
   */
  constructor() {
    super();
  }

  /**
   * The home page route.
   *
   * @class IndexRoute
   * @method index
   * @param req {Request} The express Request object.
   * @param res {Response} The express Response object.
   * @next {NextFunction} Execute the next method.
   */
  public index(req: Request, res: Response, next: NextFunction) {
    //set custom title
    this.title = "Home | Testing typescript 2, express, node";

    //set options
    let options: Object = {
      "message": "Welcome to testing typescript 2, express, node!!"
    };

    //render template
    this.render(req, res, "index", options);
  }
}
```
And add index.pug to the views directory
```
$ cat views/index.pug 
html
  head
    title= title
  body
    h1= message
```

## Finally we can compile and start the server
```
$ yarn run grunt
yarn run v0.20.0
$ grunt 
Running "copy:build" (copy) task
Copied 1 file

Running "ts:app" (ts) task
Compiling...
Cleared fast compile cache for target: app
Using tsc v2.1.5



TypeScript compilation complete: 1.38s for 4 TypeScript files.

Done.
Done in 2.10s.


$ yarn start
yarn start v0.20.0
$ node ./bin/www 
[IndexRoute::create] Creating index route.
```
And run client in the browser:
```
$ chromium http://localhost:8080
Created new window in existing browser session.
```
And we see the result in the Window:
```
Welcome to testing typescript 2, express, node!!
```

And in the server we see the access:
```
GET / 200 187.977 ms - 153
```

## The last thing Brian says is to install node monitor:
```
yarn add nodemon
```
And then you can run `dev`:
```
yarn run dev
```
Although, at the moment it doesn't seem to be doing the recompiling when I
change code.
