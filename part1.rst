==================================
Get going with Docker from Scratch
==================================

We will start out this class by installing Docker if you haven't already.

For ubuntu users this is now as simple as::

% sudo apt install docker.io

and so we don't have to sudo to use docker::

% sudo usermod -aG docker $USER

For folks not wanting to use ubuntu then you will need to head over to the docker website and look how to install for your platform here_.

.. _here: https://docs.docker.com/install/

It may also be handy to clone this repository so that you have all of the files locally accessible on your computer, change to a directory where you would want the files and run::

% git clone https://github.com/jimboid/docker-class.git


Running your first container
============================

We will first start out running the "Hello World" container. To run this container do::

% docker run hello-world

You should see the following output in your terminal


.. image:: images/docker1.png

You should be able to see that Docker could not find the container on your local machine and thus decided to pull the **image** for it from Docker Hub. 

The hello-world container simply displays a message that tells us that everything is working OK with your Docker installation, it spells out the steps it took to run it and a suggestion for further steps. But that is it, you have run your first docker container!

More Container Commands
=======================

Now you have run your first container, it would be useful if you could interrogate what containers you have installed on your system.

To list our containers we run the following::

% docker ps -a

and you should see something like this

.. image:: images/docker2.png

The information here is quite detailed. You can see the **container id** unique to each instance, the **image** tells you which container images it is deployed from. The **status** column give the container run status, in this case it will print the hello message and exit, but other services and servers may run without exiting. **ports** will tell you which physical ports on your machines you have mapped to a container port. **NAMES** gives you the friendly name generated for your container instance, either set explicitly by you or if not, randomly generated.

What happens if we run the same container again?

.. code-block:: bash

    % docker run hello-world

What does **docker ps -a** show now?

.. code-block:: bash

    % docker ps -a


It should look something like this:

.. image:: /images/docker3.png

We now have two separate instances of the hello-world container each with different container id and different names. Docker has not reused the first container to generate the second, but it did reuse the same hello-world images to generate a new instance of the hello-world container. If we wanted to reuse a container, then we use a different command.

To reuse a container we would use the following::

% docker start --attach <container name>

You should replace **<container name>** with the name listed in the names column for one of the containers from the list when listing your containers **docker ps -a**. The **--attach** flag simply tells docker to connect to the containers output so we can see the results in our terminal.

You should have seen with the Docker start command that output was the same as before, this is because you have re-executed one of the hello-world containers already deployed on your system. If you run **docker ps -a** again you'll notice that you still only have the same 2 containers deployed.

Interacting with Containers
===========================

In the previous examples, our containers exited after echoing the hello world message. This didn't really present us much chance to interact with the containers much whilst they are running. We don't need to do anything overly complicated to demonstrate these principles so lets just pull an ubuntu container and enter bash::

% docker run -it ubuntu bash

Where the **-it** flags enable us to interact with the bash terminal inside the container. You can do all the basic linux bash commands here and they work (eg **ls -al**).

Open up another terminal window alongside this one and type::

% docker ps -a

.. image:: /images/docker4.png

You see something like the above, and you will notice that the container is now running and will display an up time for as long as you don't exit the containers terminal.

If you want to see what processes are running inside a container, then you can query this by::

% docker top <container name or id>

.. image:: /images/docker5.png

We are simply running a bash terminal as the root user, that this is the only thing that will show up here.

We can stop containers simply by running::

% docker stop <container name or id>

So now running::

% docker ps -a

Should look like this. The containers are all stopped and the terminal that was using the containers internal bash terminal will have exited.

.. image:: /images/docker6.png

we can delete containers simply by doing::

% docker rm <container name or id>

or to delete all at once along with their images we can do::

% docker system prune -a

This will remove all of the container instances you have created along with the container images you downloaded to make them from, thus freeing up space.


Cheat Sheet
===========

Here are a selection of commands that are commonly useful when working with Docker. There are a lot more possibilities than those listed here, and more ways to use the ones listed here than are demonstrated. You should consult the Docker documentation for a full treatment of commands and their use.

+----------------------------------------------------------+----------------------------------------------------------+
| Command                                                  | Description                                              | 
+==========================================================+==========================================================+
| docker ps                                                | List all running containers.                             |
+----------------------------------------------------------+----------------------------------------------------------+
| docker ps -a                                             | List all container instances, with their ID and status.  |
+----------------------------------------------------------+----------------------------------------------------------+
| docker images                                            | List all images on the local machine.                    |
+----------------------------------------------------------+----------------------------------------------------------+
| docker run -p <hostport>:<containerport> -d <user/image> | Runs image in detached mode with port forwarding.        |
+----------------------------------------------------------+----------------------------------------------------------+
| docker run -it <user/image>                              | Runs image and changes into the terminal inside the      |
|                                                          | container.                                               |
+----------------------------------------------------------+----------------------------------------------------------+
| docker start <container name or id>                      | Start a container.                                       |
+----------------------------------------------------------+----------------------------------------------------------+
| docker stop <container name or id>                       | Stop a container.                                        |
+----------------------------------------------------------+----------------------------------------------------------+
| docker rm -f <container name or id>                      | Delete a container.                                      |
+----------------------------------------------------------+----------------------------------------------------------+
| docker system prune -a                                   | Delete all unused images.                                |
+----------------------------------------------------------+----------------------------------------------------------+
|docker build . -t user/image                              | Build container using Dockerfile in current working      |
|                                                          | directory.                                               |
+----------------------------------------------------------+----------------------------------------------------------+

next_

.. _next: part2.rst
