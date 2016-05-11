---
date: 2016-05-11 16:06
status: public
title: 'Docker 笔记'
---

## run
### docker run -d -P training/webapp python app.py
### -d flag which tells Docker to run the container in the background
#### -P flag is new and tells Docker to map any required network ports inside our container to our host. 
## docker stop name  
can stop container
## docker restart name
command that runs a stop and then start on the container.
### docker rm name
can’t actually remove a running container. 

### images
#### docker images 
listing the images you have locally on our host.This protects you from accidentally removing a running container you might need. You can try this again by stopping the container first.

