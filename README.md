#### Install Docker on the preferred node
`apt update -y && apt-get install docker.io -y`

#### There are client and the server of Docker, decoupled.
`docker version` or `docker info`
#### Setup Aliases, eg
`alias d='sudo docker'`
`alias stopdocks='docker stop $(docker ps -a -q)'`
`alias rmdocks='docker rm $(docker ps -a -q)'`

#### Download First Image
Check to see current images or search them:

`docker images` / `docker search <name>`

Download/pull the image 'ubuntu:14.04' / 'nginx' and list them:

`docker pull ubuntu:14.04` / `docker pull nginx:latest`
 `docker image ls`

See that the image now exists: `docker images`
See that no docker containers are running:

`docker ps -a`

Start a container: `docker run -it ubuntu:14.04`

Get a service to run within a container: `apt-get update -y && apt-get install python -y`

To `exit`. See that the container stopped: `docker ps -a`

Start a container in detached mode: `docker run -d -it ubuntu:14.04` And check it out: `docker ps -a`
To attach: `docker attach <container id>`

Commit the image: `docker commit <container id> ubuntu:python`
Observe the 'layering', eg `docker history ubuntu:python`

Set an environment variable: `docker run -d -it --name test1 -e MYSQL_PASSWORD='SecurePassword' ubuntu:14.04 bash`
See an env: `docker exec test1 env`

Map a Volume to a container:

`docker run -d -it --name test2 -v ~/data:/~/data ubuntu:14.04 bash`
`docker exec test2 ls /~/data`
`docker exec test2 cat /~/data/myfile.txt`

Start a NGINX container that forwards host port 8000 to container port 80:

`docker run -d --name web01 -h web01 -p 8000:80 nginx`

`curl localhost:8000`

Managing containers:
`docker top web01` `docker stats` `docker restart web01`

Using docker logs: 

`docker attach test`
`ls -l` `exit`
`docker logs test`

#### Inspection + Filtering:

`docker inspect web01 >> web01.json`

or

`docker inspect -f '{{.Config.Image}}' web01`

`docker inspect -f '{{.Config.Env}}' web01`

`docker inspect -f '{{.Mounts}}' db`

`docker inspect -f '{{.State.Pid}}' test2`

or Advanced filter to loop through interfaces and reveal IP address:

`docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' web01`

#### Networks

List them : `docker network ls`
Create: `docker network create frontend`
Inspect: `docker network inspect frontend`

Attach the networks to web01 container:

`docker network connect frontend web01`

Create a new container with a network attached:

`d run -d -it --network frontend --name proxy1 ubuntu:14.04`

Ping or curl it:

`d exec db1 curl web01`  / `d exec web01 ping db1`


#### Dockerfile: run an image from, eg flask app > flaskapp

`docker image build -t ubuntu:flask .`

`docker run -d --hostname webserv1 --name webserv1 -p 80:5000 ubuntu:flask`

`docker exec webserv1 ps`

`curl localhost`


#### docker-compose.yml > compose

Install compose on a necessary node:

`sudo apt-get install docker-compose -y` | `sudo docker-compose version`

Create a docker-compose.yml and then:

`sudo docker-compose up -d`

Check it out: `sudo docker-compose ps` | `sudo docker-compose images` | `curl localhost:5000`

See the logs:   `sudo docker-compose logs`
Scale it up: `sudo docker-compose up -d --scale db=2`

#### Swarm

`sudo docker swarm init` | `sudo docker swarm init --advertise-addr < IP Address >`

To connect another node to the swarm, first install docker there and then join it with a given token, eg

`sudo docker swarm join --token SWMTKN-1-4d4i1hd93vgcq8ye0i6moff83t388fveqpsrdofi4c32dws4m5-d12yh220jwwreb2hjc0w2umc8 192.168.56.10:2377`

Create a service:

`sudo docker service create --replicas 1 --name pingdocker ubuntu:14.04 ping docker.com`

Check it: `sudo docker service ls` | `sudo docker node ps` | `sudo docker node ps node2`

Scale: `sudo docker service scale pingdocker=2`


