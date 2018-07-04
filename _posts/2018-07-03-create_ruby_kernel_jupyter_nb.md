---
title: "Ruby Kernel Jupyter Notebook"
date: 2018-07-03
tags: [Jupyter Notebook, Ruby, Docker]
excerpt: "Jupyter Notebook, Ruby, Docker"
---

# How to create Jupyter Notebook with Ruby Kernel

### Initial Idea 

Jupyter Notebooks are a very good option to provide a pre configured programming environment which could be easily used for coding tutorials or simple code testing. 



### Docker Image

As there is a lot of OS dependencies and pre OS configuration I will create a Docker image which could be easily provided and distributed. 

Here is my basic Dockerfile with the inital OS configuration and installed packages: 


```
FROM ubuntu:17.10

# Then install python3 and pip3
RUN apt-get update && apt-get install -y python3 \
    python3-pip

# Install jupyter
RUN pip3 install jupyter

# Create a new system user
RUN useradd -ms /bin/bash jupyter

# Change to this new user
#USER jupyter

# Set the container working directory to the user home folder
#WORKDIR /home/jupyter

#Preparing Dependencies

RUN apt-get update && apt-get install -y libtool libffi-dev ruby ruby-dev make
RUN apt-get update && apt-get install -y libzmq3-dev libczmq-dev


RUN gem install cztop iruby
RUN iruby register --force

# Start the jupyter notebook
ENTRYPOINT ["jupyter", "notebook", "--ip=*"]


```

I'm using Ubunut 17.10 as base image as it is easy to configure for the Ruby kernel. 
At first python and jupyter notebooks gets installed, afterwards the ruby specific gems are installed. 

Most important 

* [iruby](https://github.com/SciRuby/iruby)

Create the image: 

```
docker build . -t [imagename]

```

Start Docker container: 
```
docker run -it -p 8888:8888 [imageID] --allow-root
```

Now the notebook is available under: 

http://localhost:8888/?token=GIVEN_TOKEN


