How to write Dockerfile
=======================
we create docker file debian 9 and add nodejs ontop then we have base image for microservice::

    FROM debian:latest
    MAINTAINER sawangpong@itbakery.net 
    RUN apt update -y
    RUN apt install curl software-properties-common gnupg2 -y
    RUN curl -sL https://deb.nodesource.com/setup_9.x | bash -
    RUN apt  install nodejs build-essential libssl-dev -y

run::

    docker built -t debian_nodejs:0.1 .
    docker images -a
    docker history debian_nodejs:0.1
    docker system prune

Dockerfile each line is  command and argument docker will run command line by line.
each line will create the layer. docker will cacheing them if we run secondtime docker will 
pull from cache and check their is any change on line if not docker will skip it. 

