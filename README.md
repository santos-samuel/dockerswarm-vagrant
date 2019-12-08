AUTO_START_SWARM=true vagrant up
vagrant status
vagrant ssh manager
docker node ls

CONFIGURE ANSIBLE
LAB4
ssh-keyscan manager worker1 worker2 >> .ssh/known_hosts
ssh-keygen -t rsa -b 2048
ansible-playbook ssh-addkey.yml --ask-pass
vagrant vagrant
ansible all -m ping

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
docker-compose up -d
curl localhost
docker cp nginx_templates/index.html nginx_example2_webserver_1:/usr/share/nginx/html/index.html
curl localhost

now that the created node has the desired config lets create an image of it:
docker ps -a

We can see a new container which is the container that from the last service

Copy the name of the moo nginx_moo_webserver container and create an image of it:
docker commit #containerName nginx_moo

docker images -> updated

Now we can stop and remove the container used to configure:
docker ps stop tab
docker ps rm tab

Locally created image so, we need to share the image with all the swarm nodes! To do that:


And do a rolling update on our first service which has the default nginx page:
docker service update --image nginx_moo:latest nginx1_webserver

docker service ps nginx_sample_webserver