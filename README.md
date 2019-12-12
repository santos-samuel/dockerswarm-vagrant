AUTO_START_SWARM=true vagrant up

vagrant status

vagrant ssh manager



CONFIGURE ANSIBLE

LAB4

ssh-keyscan manager worker1 worker2 >> ~/.ssh/known_hosts

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



Now that we have our image we have to:

 1- publish our image so that other nodes can retrieve it

  2- other nodes need to pull the image to their docker images

  3- update the service to use the latest image



Aproveitamos este caso para mostrar como usar o ansible com o Docker:

To do that we created an Ansible Playbook. (equivalente a fazer o que está lá em baixo)

ansible-playbook loadimage.yml



docker service ps nginx_sample_webserver

curl 192.168.56.2

curl 192.168.56.3

curl 192.168.56.4



vagrant halt

vagrant global-status

DONE

------------------------------------------------------------------------------------------------------------

------------------------------------------------------------------------------------------------------------

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



Ansible playbook by hand:

Fazer em todos os nós:

/etc/docker/daemon.json

{

	    "insecure-registries": ["192.168.56.2:5000"]

    }

    sudo service docker restart





docker run -d -p 5000:5000 --restart=always --name registry registry:2

docker tag nginx_moo 192.168.56.2:5000/nginx_moo

docker push 192.168.56.2:5000/nginx_moo



docker pull 192.168.56.2:5000/nginx_moo

docker service update --image 192.168.56.2:5000/nginx_moo nginx_sample_webserver
