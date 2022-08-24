# Rails and MongoDB development with Docker

## Create a local image for latest version of rails

```
$ docker run -it ruby /bin/bash
```
Within the container do the following
```
# apt-get install vim iputils-ping
# gem install rails
# ... create bashrc, gitconfig etc manually
```

now create the image by specifying the container id

```
$ docker commit c0561d2d4998 rails:7
```

## Create network

```
$ docker network create localdev
```


## Start mongo instance

```
$ docker run -d --name mongo --network localdev -p 27017:27017 mongo
```

## Start rails container

```
$ docker run -it -p 3000:3000 --name app --network localdev --volume `pwd`:/app rails:7 /bin/bash
```

## Setup

from within the rails container, execute the following commands to start the rails server:

```
$ cd /app
$ bundle install
$ rails s -b 0.0.0.0
```
