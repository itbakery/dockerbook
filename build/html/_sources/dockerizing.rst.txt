Dockerizing Django
==================
1 Dockerizing django application with gunicorn.

    * Create new  projectdirectory "proj_openthai" 
    * Create sub directory "config" inside
    * Create virutual environment for python3
    * Create Django project "openthai"
    * Create Dockerfile 

start project::

    mkdir proj_openthai  && cd $_
    mkdir config
    python3 -m venv venv3
    source venv3/bin/activate
    pip install django gunicorn psycopg2
    pip freeze > config/requirements.pip 
    

create Dockerfile::

    FROM python:3.6.4-alpine3.7
    ENV PYTHONUNBUFFERED 1  
    RUN mkdir /config  
    RUN set -ex \
        && apk add --no-cache --virtual .fetch-deps \
            postgresql-dev \
            gcc \
            python3-dev \
            musl-dev \
            py3-psycopg2
    ADD /config/requirements.pip /config/  
    RUN pip install -r /config/requirements.pip  
    RUN mkdir /src 
    WORKDIR /src  