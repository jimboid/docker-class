============================
Building your own containers
============================

In the following examples, rather than demonstrate the usual "how to deploy a basic webserver" that is common in all basic docker workshops. We are going to build a fully deployable JupyterHub server that is capabale of hosting your own notebooks, either on the cloud, a server or your own PC. I've been informed that Jupyter is a big part of these classes and that you will all hopefull have notebooks that you can use already to hand, so we will try to use these in this exercise.

Build the base container
========================

The first part of this exercise is to simply build the base container. So change into the directory **jupyterbase** and run the following::

% docker build . -t jupyterbase

Your base container should now be building. This is going to be a while!!

Whilst this is building let me explain what this container will consist of.

Take a peek inside the Dockerfile::

% nano Dockerfile

You will see that in building this container, we are basically starting out from a base image of ubuntu 18.04 (bionic). Then performs an update of all system packages before installing some basic utilities such as compilers etc. Before moving onto installing package managers such as python pip and miniconda. It then creates a non root user and deploys the JupyterHub server instance under this user.

If your base container is still building, you can move onto setting up the next container whilst you wait.

Make the notebook container
===========================

Having base containers that consist of basic systems such as servers, means that you can quickly setup up and deploy new containers that build on top of these to introduce functionality. You can have many of these functionality based containers built ontop of one common base.

Here we are going to make a container that will install one/more of your own Jupyter notebooks into the JupyterHub server we created in the previous container.

To run your notebooks, you will need to install any dependencies that your notebooks need as well as copying your notebooks from your host machine into your containers.

Firstly, make a new directory "first-notebook" copy your jupyter notebook files into this directory then change into it and create a docker file and open it for editing:

.. code-block:: bash

    % mkdir first-notebook
    % cd first-notebook
    % touch Dockerfile 
    % nano Dockerfile

Now copy and paste the following template into your Dockerfile:

.. code-block:: bash

    # Start with our base container.
    FROM jupyterbase:latest

    # Feel free to change this.
    LABEL maintainer="James Gebbie-Rayet <james.gebbie@stfc.ac.uk>"

    # Root to install "rooty" things
    USER root

    # Update and Install anything from apt <delete if not necessary>.
    RUN apt update -y 
    RUN apt install -y csh

    # Back to jovyan user (Important!)
    USER $NB_USER

    # Replace these with your actuall dependencies, use conda install if you prefer!
    RUN pip install --user numpy
    RUN pip install --user cython
    RUN pip install --user scipy
    RUN pip install --user pandas
    RUN pip install --user matplotlib

    # Add all of the notebook files to the home directory.
    ADD --chown=jovyan:100 *.ipynb $HOME/

    # If your notebook has a data directory or files then copy these too
    # (modify this to match your case).
    ADD --chown=jovyan:100 data $HOME/data

    # Always finish with non-root user as a precaution.
    USER $NB_USER

Modify the template so that it will install your dependencies for your notebook. You only really need to modify the **sudo apt** part for packages installed from aptitude, the pip lines to pip install python dependencies or if you prefer use conda install instead. Once done, and if your base container has finished building run::

% docker build . -t first-notebook

And once finished building, run an instance::

% docker run -p 8888:8888 first-notebook

Once run, in the terminal, a link should be created to launch JupyterHub. Click this link or copy it to a local web-browser and you will see your Jupyter notebook working (if the dependencies are all working).

Thats it! You have just deployed a fully cloud deployable multi-user Jupyter notebook server. We actually use this approach in conjunction with Microsoft Azure to deliver all of our community training resources, whether be "how to do MD" through to "basic python coding" workshops. 

But you could use it to have a fully isolated Jupyter notebook environment that is reproducible on machines that you deploy to, and introduce a full CI/CD toolchain to validate your code.


Previous_

.. _previous: part1.rst
