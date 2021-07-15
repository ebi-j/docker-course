# Docker Course Notes

## What is Docker?

A platform for building, running and shipping applications in a consistant manner.  
Provides a isolated environment with all the dependencies packaged.  
Solve the "it works on my machine" problem.  

Remove all unused depdencies to clean up the machine:

```console
docker-compose down --rmi all
```

## Container VS MV

Container: an isolated environment for running an application.  
VM: an abstraction of a real machine (physical hardware).

Each VM needs an entire OS, slow to start, resource intensive.  
Container provides similar isolated environment to run apps, but lightweight, sharing the same OS (Kernel) of the host.

## Archetecture

Client - server archeture, client calls the server via REST API, the server is also called Docker Engine, building and running containers. Containers are processes.

Windows 10: Windows Kernel + Linux Kernel  
Linux: Linux Kernel  
Mac: Linux VM

## Development Workflow

Add a dockerfile in the application. A dockerfile includes instructions that Docker uses to package the application into an image.

* A cut-down OS
* A runtime envrionment (e.g. Node)
* Application files
* Third-party libraries
* Environment variables

Image gets loaded in a container.  
Image can be pushed up to Registry (like GitHub to Git).

```dockerfile
# This is to choose what image we're going to use, in this case, Node.
# ":alpine" is a specific Linux distribution we want to use.
FROM node:alpine
# Copy all the application file to folder "app".
COPY . /app
# Define the working directory as "/app".
WORKDIR /app
# Run the command we want.
CMD node app.js
```

To let Docker build the application:

```console
docker build -t hello-docker .
```

`-t` defines the tag of the build (it will also be the repository name, which will be used to run the app), `.` lets Docker know that the dockerfile is in the current directory.

To check all the images on the computer:

```console
docker image ls
```

or 

```console
docker images
```

To let Docker run the app:

```console
docker run hello-docker
```

## Linux Command Line

To see running docker processes:

```console
docker ps
```

To see all docker processes:

```console
docker ps -a
```

To run app interactively:

```console
docker run -it ubuntu
```

`-it` means interactive.

### apt

Advanced Package Tool (in Linux)

To update apt database:

```console
apt update
```

To install a package:

```console
apt install nano
```

To list the database:

```console
apt list
```

To remove a package:

```console
app remove nano
```

### Navigating the File System

`pwd` to print the working directory:.

`ls` list, `ls -1` one record a line, `ls -l` list with long records. `li [path]` list a directory.

`cd ~` to home directory.

`mkdir [directory name]` to create a directory.

`mv` move or rename folder or files.

`touch` to create a or multiple files.

`rm` to remove files, `rm -r` to recursively remove files and folders.

`cat` to view files. `more` for long file. `less` for long file with up and down options (need to install less from apt). Press `Q` to quit. `head` shows the first a few lines of the file with option `-n`. `tail` shows the last a few lines of a file.

