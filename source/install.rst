Install Docker on Centos 7
==========================
install with script on linux
::

  yum update -y
  curl -sSL https://get.docker.com/ | sh
  curl -L https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
  chmod +x /usr/local/bin/docker-compose

  systemctl enable docker
  systemctl start docker
  
  docker -v
  Docker version 17.12.0-ce, build c97c6d6
  
  docker-compose -v
  docker-compose version 1.18.0, build 8dd22a9

  yum groupinstall "Development Tools"
  yum -y install zlib-devel
  yum -y install gcc gcc-c++ 
  yum -y install zlib zlib-devel
  yum -y install libffi-devel 
  wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0a3.tgz
  tar xvf Python-3.7.0a3.tgz
  cd 
  ./configure --enable-optimizations
  make 
  make altinstall
  
  python3.7
  Python 3.7.0a3 (default, Jan  2 2018, 20:58:46)
  [GCC 4.8.5 20150623 (Red Hat 4.8.5-16)] on linux
  Type "help", "copyright", "credits" or "license" for more information.


  pip3.7 --version
  pip 9.0.1 from /usr/local/lib/python3.7/site-packages (python 3.7)

  
