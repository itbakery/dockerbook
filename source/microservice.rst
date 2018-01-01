What we will see today
======================

    * Create base docker image (alpine+node  ubuntu+node)
    * Build a simple micro-service locally (base image + microservice)
    * Run our micro-service inside a container
    * Create Docker image from an exits containe
    * Publish our image in Docker hub

::

    mkdir proj_microservice
    cd proj_microservice
    docker pull node:9.3.0-alpine
    docker run -i -t node:9.3.0-alpine /bin/sh

create script `install.sh` ::

    apk add --update nodejs nodejs-npm
    exit 
    docker ps -a
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                          PORTS               NAMES
    1c58c6793db4        node:9.3.0-alpine   "sh"                     16 minutes ago      Exited (0) About a minute ago                       objective_heyrovsky

    docker commit -a itbakery 1c58c6793db4  node_alpine:1.0
    sha256:46fbcd7158d19e6c507334ad6cdeb1f335e77d674500dbb02e941fa3b94208af

    docker images
    REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
    node_alpine              1.0                 46fbcd7158d1        16 seconds ago      99MB
    
now we have base image ``docker commit -a "author" container_id  image_name:tag``

Build a simple micro-service
****************************
crate microservice inside directory proj_microservice test and run before port to docker 
::

    npm install -g express-generator
    express microservice

structure now::

    .
    └── microservice
        ├── app.js
        ├── bin
        │   └── www
        ├── package.json
        ├── public
        │   ├── images
        │   ├── javascripts
        │   └── stylesheets
        │       └── style.css
        ├── routes
        │   ├── index.js
        │   └── users.js
        └── views
            ├── error.jade
            ├── index.jade
            └── layout.jade

result

    * check file package.json start script is ``/bin/www``::

        {
        "name": "microservice",
        "version": "0.0.0",
        "private": true,
        "scripts": {
            "start": "node ./bin/www"
        },
        "dependencies": {
            "body-parser": "~1.18.2",
            "cookie-parser": "~1.4.3",
            "debug": "~2.6.9",
            "express": "~4.15.5",
            "jade": "~1.11.0",
            "morgan": "~1.9.0",
            "serve-favicon": "~2.4.5"
        }
        }


    * www script load app.js and start listening http::

        var app = require('../app');
        var debug = require('debug')('microservice:server');
        var http = require('http');

        var port = normalizePort(process.env.PORT || '3000');
        app.set('port', port);

        var server = http.createServer(app);

        server.listen(port);


    * app.js load 2 module and use it::

        var index = require('./routes/index');
        var users = require('./routes/users');

        app.use('/', index);
        app.use('/users', users);

    * add routers/api.js::

        var express = require('express');
        var router = express.Router();

        /* GET hello. */
        router.get('/sayhello', function (req, res, next) {
            res.send('Hello');
        });

        module.exports = router;

    * add::

        var index = require('./routes/index');
        var users = require('./routes/users');
        var api = require('./routes/api');

        app.use('/', index);
        app.use('/users', users);
        app.use('/api', api);

    * npm build and start::

        npm install
        npm start
        > microservice@0.0.0 start /Users/mee/Work/proj_microservice/microservice
        > node ./bin/www

    * open browser   localhost:3000/api/sayhello


Port service to docker image::

    docker images
    docker run -it -v "$(pwd)":/host  -p 9000:3000 node_alpine:1.0 ./bin/bash
    / # ls /host/
    app.js             node_modules       package.json       routes
    bin                package-lock.json  public             views

    cp -r /host  /microservice 
    cd /microservice
    npm start

    (test by open browser to localhost:9000)

    exit 


* -v mount host  to docker. it's temporary for test. remember we have to copy to container
* -p port host and docker
* $(pwd) use current working directory

commit create new image::

    docker ps -a
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS                    NAMES
    27520ba79596        node_alpine:1.0     "./bin/sh"               24 minutes ago      Exited (0) 8 seconds ago                            hardcore_nightingale

    docker commit -a itbakery 27520ba79596 node_alpine_service:1.0
    sha256:133a5fbd577d7ebdcb85be4044ca93eeabb0d7b3445b5f7a5204f52dea97f9a9

    docker images
    REPOSITORY               TAG                 IMAGE ID            CREATED              SIZE
    node_alpine_service      1.0                 133a5fbd577d        About a minute ago   105MB
    node_alpine              1.0                 46fbcd7158d1        About an hour ago    99MB

    docker run -d -w /microservice -p 9000:3000 node_alpine_service:1.0 npm start
    1212da18fad5a240a84dddfc01681e9bbacb4483cbc68f531c6decdd78ffafb7


* -w  working directory in container 
* -p  port map
* npm start  run this command in docker at working directory 

add to repository::

    docker tag node_alpine_service:1.0  node_alpine_service:latest
    docker images
    REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
    node_alpine_service      1.0                 133a5fbd577d        9 minutes ago       105MB
    node_alpine_service      latest              133a5fbd577d        9 minutes ago       105MB

* latest allow user only pull with name::

    docker tag node_alpine_service:  itbakery/node_alpine_service
    docker images
    REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
    itbakery/node_alpine_service   latest              133a5fbd577d        12 minutes ago      105MB
    node_alpine_service            1.0                 133a5fbd577d        12 minutes ago      105MB
    node_alpine_service            latest              133a5fbd577d        12 minutes ago      105MB
    node_alpine                    1.0                 46fbcd7158d1        2 hours ago         99MB        
        
push::

    docker login
    Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
    Username: itbakery
    Password:
    Login Succeeded

    docker push itbakery/node_alpine_service
    The push refers to a repository [docker.io/itbakery/node_alpine_service]
    1286cbe57360: Pushed
    ed703467fa41: Pushed
    fe9c7b6dc22a: Pushed
    6b7b1fa2531c: Pushed
    6dfaec39e726: Pushed

    latest: digest: sha256:21b62028e6809023c13c7116677f89f3e1da892498013a66f44c551698cf3a25 size: 1374