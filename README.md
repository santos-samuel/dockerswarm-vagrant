AUTO_START_SWARM=true vagrant up
vagrant status
vagrant ssh manager

CONFIGURE ANSIBLE
LAB4
ssh-keyscan manager worker1 worker2 >> .ssh/known_hosts
ssh-keygen -t rsa -b 2048
ansible-playbook ssh-addkey.yml --ask-pass
vagrant
ansible all -m ping


docker node ls
But no service running
docker service ls


nginx_example_1:
docker stack deploy -c docker-compose.yml nginx_sample
docker service ls
docker service ps nginx_sample_webserver

If you:
curl 192.168.56.2 
or 
curl 192.168.56.2:80
from inside any of the clusters you should get our webpage.

However, this homepage sucks
Lets change it
Create a new container with the same image (ideally in a node that is not in the swarm)
docker-compose -f docker-compose-2.yml up -d
curl localhost:80 (nginx_sample container)
curl localhost:8000 (nginx_config container)
docker cp index.html nginx_config:/usr/share/nginx/html/index.html
curl localhost:8000 (nginx_config container)

now that the created node has the desired config lets create an image of it:
docker ps -a

We can see two containers. One is the container that is running the service on the swarm
The other is the one we created locally to change and changed config.

Lets create an image from the edited container:
docker commit nginx_config nginx_moo

docker images -> updated

Now we can stop and remove the container used to configure:
docker stop nginx_config
docker rm nginx_config
docker ps -a

Locally created image so, we need to share the image with all the swarm nodes! To do that:
docker run -d -p 5000:5000 --restart=always --name registry registry:2
ansible-playbook loadimage.yml
this will:
	1- publish the nginx_moo image on our local registry

Now that all the nodes have the updated image we can do a rolling update
docker service update --image localhost:5000/nginx_moo nginx_sample_webserver

docker service ps nginx_sample_webserver
curl 192.168.56.2
curl 192.168.56.3
curl 192.168.56.4


CLEAN DOCKER:
docker system prune
docker system prune -a
docker images -f dangling=true
docker images purge
docker images -a | grep "pattern" | awk '{print $3}' | xargs docker rmi
docker rmi $(docker images -a -q) -f
docker rm $(docker ps -a -f status=exited -q) -f
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)