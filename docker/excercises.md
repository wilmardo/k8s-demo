# Docker Excercises

These exercises are a bit sparse and will be expanded in the future :)

## 1. Practice with Docker

### Prerequisite

First and foremost setup Docker, the installation instructions can be found here:
https://docs.docker.com/install/#supported-platforms

### Your first Docker image

In the current directory is a file named Dockerfile, this is the specification for your Docker image.
To build this image from the Dockerfile use the docker build command. You should pass the current directory as context to the command ( . == current directory).
The docker build command then looks for a file named Dockerfile in this folder:

`docker build .`

Now you will see the image building and the build should result in something like `Successfully built 480598624b79`.
This last generated string is the ID of the built image and we can use it to run the image:

`docker run 480598624b79`

As you execute the command you will see the Flask app starting and listening on http://0.0.0.0:5000/

But this generated string is inconvinient for any users and will differ each time you rebuild the image. Herefore you can tag an image to be more easy to use:

`docker tag 480598624b79 hello-world`

Now that we have tagged the image we can simply run the image with that tag:

`docker run hello-world`

Way more convinient right? It gets even better! You can tag the image while building to avoid the generated string altogether:

`docker build . -t hello-world`

This will build the image and tag it with hello-world so it can be run with the hello-world tag.

`docker run hello-world`

Leave the container running and try to visit http://127.0.0.1:5000 (since the container runs on the localhost). It will timeout because Docker has not forwarded the port.
With the `-p` you can specify what containerports need to be forwarded to the host in the `hostport:containerport` format:

The following command will forward port 5000 (Flask app) to port 7000 on the host:

`docker run -p 7000:5000 hello-world`

Now, when you visit http://127.0.0.1:7000 you should be presented with `Hello World!`
