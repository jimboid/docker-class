# This is the base container for the CCPBioSim cloud based training platform.

# Start with Ubuntu 18.04.
FROM ubuntu:bionic

LABEL maintainer="James Gebbie-Rayet <james.gebbie@stfc.ac.uk>"

# Do the root stuff.
USER root

# Set non-interactive front end (use ARG instead of env).
ARG DEBIAN_FRONTEND=noninteractive

# Update ubuntu and install some common tools.
RUN apt-get update && apt-get -yq dist-upgrade \
 && apt-get install -yq --no-install-recommends \
    bc \
    bzip2 \
    ca-certificates \
    cmake \
    gcc \
    git \
    gfortran \
    g++ \
    less \
    fonts-liberation \
    libgfortran3 \
    locales \
    make \
    nano \
    openssh-client \
    python3 \
    python3-pip \
    python3-setuptools \
    rsync \
    sudo \
    wget \
    zlib1g-dev \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Update pip to the latest version.
RUN python3 -m pip install --upgrade pip

# Set container locale.
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen

# Install Tini - A tiny but valid init for containers https://github.com/krallin/tini.
RUN wget --quiet https://github.com/krallin/tini/releases/download/v0.10.0/tini && \
    echo "1361527f39190a7338a0b434bd8c88ff7233ce7b9a4876f3315c22fce7eca1b0 *tini" | sha256sum -c - && \
    mv tini /usr/local/bin/tini && \
    chmod +x /usr/local/bin/tini

# Set up user environment variables.
ENV NB_USER=jovyan \
    NB_UID=1000 \
    NB_GID=100 \
    LC_ALL=en_US.UTF-8 \
    LANG=en_US.UTF-8 \
    LANGUAGE=en_US.UTF-8

# Path environment variables.
ENV CONDA_DIR=/opt/miniconda \
    SHELL=/bin/bash \
    HOME=/home/$NB_USER

# Export important paths.
ENV PATH=$CONDA_DIR/bin:$PATH
ENV PATH=/home/jovyan/.local/bin:$PATH

# Create non-root user.
RUN useradd -m -s $SHELL -N -u $NB_UID $NB_USER

# Install Miniconda.
RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O /tmp/miniconda.sh && \
    chmod a+x /tmp/miniconda.sh && \
    bash /tmp/miniconda.sh -b -p /opt/miniconda && \
    chown -R $NB_USER:$NB_GID /opt/miniconda && \
    rm /tmp/miniconda.sh

# Use tini to launch the JupyterHub startup script.
ENTRYPOINT ["tini", "--"]
CMD ["start-notebook.sh"]

# Make sure that the Jupyter notebook port is accessible.
EXPOSE 8888

# Copy in the JupyterHub setup and configuration files.
COPY start.sh /usr/local/bin/
COPY start-notebook.sh /usr/local/bin/
COPY start-singleuser.sh /usr/local/bin/
COPY jupyter_notebook_config.py /etc/jupyter/

# Change to jovyan user to install JupyterHub and any user space utils.
USER $NB_USER
WORKDIR $HOME

# Install Jupyter Notebook and Hub and other useful packages
RUN conda config --system --prepend channels conda-forge && \
    conda config --system --set auto_update_conda false && \
    conda config --system --set show_channel_urls true && \
    conda install --quiet --yes 'notebook=5.2.*' 'jupyterhub=0.8.*' 'jupyterlab=0.31.*'

# Install the JupyterHub authenticator package.
RUN pip install jupyterhub-tmpauthenticator

RUN conda install tornado=4.5.3 && \
    conda clean -tipsy && \
    rm -rf $CONDA_DIR/share/jupyter/lab/staging && \
    rm -rf $HOME/.cache $HOME/.jupyter $HOME/.local/share/jupyter

# Always finish with non-root user as a precaution.
USER $NB_USER

