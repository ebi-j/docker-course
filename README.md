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

### Redirection

`cat` stands for concatenate.  
`cat [file1] > [file2]` read file1 and write to file 2. `cat [file1] [file2] > [file3]` read file1 and file2 and write to file3. `echo hello > [file]` write a line of "hello" to file. `ls -l [directory] > [file]` write long list result of directory and write the results to file.  
`>>` appends data instead of overwriting the file.

### Searching for Text

`grep` stands for global regular expression print.  
`grep hello [file]` Search hello in file. 
`grep -i` case insensitive.  
`grep -r` recursively. To combine together, `grep -ir`.

`find` shows all files and directories of the current directory including hidden ones. `-type d` only for directories, `type f` only for files. `-name` name pattern.

### Chaining Commands

Use `;`. The error command will not affect the rest of the commands.

```console
mkdir test;cd test;echo done
```

Use `||`. When a command fails the rest commands will execute, otherwise, the rest commands will not execute.

```console
mkdir test||echo "directory exists"
```

Use `|` to create a pipe. `ls /bin | less` to send the output of the list of `/bin` and view it with command `less`. 

Use `\` to start a new line in chainning commands.

```console
mkdir test;\
cd test;\
echo done
```

### Environment Variables

`printenv` to print all the environment variables. `printenv [name]` prints a certain environment variable, or use `echo $[name]`.  
`export` to set an environment variable in the current terminal session (no longer exists when the terminal is turned off). If we want to persist a variable, write it to .bashrc: `echo DB_USER >> .bashrc`. `.bashrc` is loaded only once, to reload, `source .bashrc` (need to be in the home directory to run this command).

### Managing Processes

`ps` to list the processes. 
`sleep 3` to sleep prompt for 3 seconds. `sleep 3 &` will be running at the background.  
`kill [PID]` to kill a process.

### Managing Users

`useradd` to add a new user. `usermod` to modify a user. `userdel` to delete a user.  
`useradd -m [username]` create a user with a home directory.  
`usermod -s /bin/bash [username]` modify user's shell program to bash.  
`userdel [username]` delete the user.
`adduser [username]` will create a user with the group created, home directory created, and allow you to set the password and other information.

To log in as another user from Docker:
```console
docker exec -it -u [username] [process id] bash
```

### Managing Groups

`groupadd [group name]` to add a new group.
`usermod -G [group name]` to add a user to a group or groups, with "," to separate group names.
`usermod -g [group name]` to add a user to a primary group.

### File Permissions

`chmod` to change permissions for file onwer, group, and public.  
`chmod u+x` to add execute permission to the file owner.  
`chmod o+x` to enable the permission to others.  
`chmod g-x` to disable execute permission to group.

## Docker Images

Image includes everything that an app needs to run. Container gives an isolated environment to execute the app, it's just a process.

`ADD` acts the same as `COPY` except for two addtional features: load resource from a URL, and uncompress a compressed file.

`.dockerignore` is used to exclude all the folders and files from the build.

`ENV` is to set the environment variables.

`EXPOSE` is state which port Docker should listen to.

`RUN` is executed when building the image, `CMD` instruction is a runtime instruction.  
`CMD npm start` runs in a shell, in Linux, /bin/sh, in Windows, cmd, `CMD ["npm", "start"]` executes directly.
`ENTRYPOINT` can only be overwritten with `--entrypoint` option, while `CMD` can be easily overwritten in Docker CLI.

When writing a dockerfile, always have stable instructions staying at the top, so they can be cached for the performance purpose, and then the changing instructions.

Dockerfile for a React app image:

```dockerfile
FROM node:14.16.0-alpine3.13
# Add an app user instead of using the root user
RUN addgroup app && adduser -S -G app app
# Set the user who is going to login and do the operations
USER app
WORKDIR /app
# Copy package json first, so installing dependencies can be cached
COPY package*.json .
RUN npm install
COPY . .
# Define enviroment variable
ENV API_URL https://localhost:3001
# Define exposing to which port
EXPOSE 3000
# Set up the entrypoint command
ENTRYPOINT ["npm", "start"]
```

`Loose images` dangling images with no tag name and no repository name. To get rid of them: `docker container prune` and `docker image prune`.
