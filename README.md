# Jupyter with Docker Compose

This repository contains a simple docker-compose definition for launching the popular Jupyter Data Science Notebook.
You can define a password with the script ```generate_token.py -p S-E-C-R-E-T``` and generate SSL certificates as described below.

## Control the container:

* ```docker-compose up``` mounts the directory and starts the container
* ```docker-compose down``` destroys the container

## The compose file: docker-compose.yml

```bash
version:  '3'
services:
  datascience-notebook:
      image:    jupyter/datascience-notebook
      volumes:
        - ${LOCAL_WORKING_DIR}:/home/jovyan/work
        - ${LOCAL_DATASETS}:/home/jovyan/work/datasets
        - ${LOCAL_MODULES}:/home/jovyan/work/modules
        - ${LOCAL_SSL_CERTS}:/etc/ssl/notebook
      ports:
        - ${PORT}:8888
      container_name:   jupyter_notebook
      command: "start-notebook.sh \
        --NotebookApp.password=${ACCESS_TOKEN} \
        --NotebookApp.certfile=/etc/ssl/notebook/jupyter.pem"
```

## Example with a custom user

```YAML
version: '2'
services:
    datascience-notebook:
        image: jupyter/base-notebook:latest
        volumes:
            - /tmp/jupyter_test_dir:/home/docker_worker/work            
        ports:
            - 8891:8888
        command: "start-notebook.sh"
        user: root
        environment:
          NB_USER: docker_worker
          NB_UID: 1008
          NB_GID: 1011
          CHOWN_HOME: 'yes'
          CHOWN_HOME_OPTS: -R

```
## The environment file .env

```bash
# Define a local data directory
# Set permissions for the container:
#   sudo chown -R 1000 ${LOCAL_WORKING_DIR}

LOCAL_WORKING_DIR=/data/jupyter/notebooks

# Generate an access token like this
#   import IPython as IPython
#   hash = IPython.lib.passwd("S-E-C-R-E-T")
#   print(hash)
# You can use the script generate_token.py

ACCESS_TOKEN=sha1:d4c78fe19cb5:0c8f830971d52da9d74b9985a8b87a2b80fc6e6a

# Host port
PORT=8888

# Provide data sets
LOCAL_DATASETS=/data/jupyter/datasets

# Provide local modules
LOCAL_MODULES=/home/git/python_modules

# SSL
# Generate cert like this:
#   openssl req -x509 -nodes -newkey rsa:2048 -keyout jupyter.pem -out jupyter.pem
# Copy the jupyter.pem file into the location below.
LOCAL_SSL_CERTS=/opt/ssl-certs/jupyter
```



# Version Conflicts

Make sure to have the latest versions installed. You can use the Notebook Browser interface.
```python
pip install -U jupyter
```
