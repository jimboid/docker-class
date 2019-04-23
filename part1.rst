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

Running your first container
============================

We will first start out running the "Hello World" container. To run this container do::

% docker run hello-world

You should see the following output in your terminal.


.. image:: images/docker1.png
