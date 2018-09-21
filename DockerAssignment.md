Problem Statement

    • Dynamic Allocation of Linux systems for users.
    
    • Each user should have independent Linux System.
    
    • Specific training environment should be created in Container.
    
    • User should not allow to access other containers/images.
    
    • User should not allow to access docker command.
    
    • Monitor participants containers.
    
    • Debug/live demo for the participants if they have any doubts/bug in running applications.
    
    • Automate container creation and deletion.

Creating a training environment

    1. We will be using ubuntu container.
    2. Run the command
       	docker pull ubuntu
    3. Then run the command
       	docker run -it --name traincont -it ubuntu /bin/bash
       to create a new ubuntu container.
    4. Type the command exit to exit the shell.

Allocating container for each user
    1. To create a container for a single user with user id usernamefoo the command is
       	docker create -it --name abc training:v1 /bin/bash
    2. This creates a container with name of usernamefoo from the image training:v1, but doesn't start the container.
    3. Allocation of containers can be automated when a file with the list of users is given. Let the file containing user ids be users
       	user01
       	user02
       	user03
       	user04
              
       Script for allocating containers to users:
       #!/bin/sh
       
       file=$1
       
       # If filename not passed exit
       if [ -z "${file}" ]
       then
       	echo "Usage: allocate <filename>"
       	exit
       fi
       
       while read user || [[ -n "$user" ]]
       do
       	container_id="$(docker create -it --name $user training:v1 /bin/bash)"
       	echo "$user: $container_id"
       done < "$file"
    4. We can run the command allocate userfiles to allocate a container for every user id in userfiles.

Using an allocated container
    1. Now that the container has been allocated for each user we need to start it and attach the container (attach local standard input, output, and error streams to the running container).
    2. The container for user with id usernamefoo can be used by running the following commands:
       	docker start usernamefoo # Starts container usernamefoo
       	docker attach usernamefoo # Attaches local stdin, stdout and stderr to usernamefoo
       
Run the above commands so that the user gets access to the shell of his system.

Safety of containers
The above commands isolate the containers completely. A user cannot access another user's container from within.

Monitoring the containers
    1. To monitor the usage container usernamefoo, run the command
       	docker stats usernamefoo
    2. To view the logs of container usernamefoo, run the command
       	docker logs usernamefoo
    3. To view the realtime logs of container usernamefoo, run the command
       	docker logs -f usernamefoo

Conclusion
Containers provide an efficient way to manage isolated systems and make it easy to monitor the systems. Isolation provides safety to the host system and other containers.
