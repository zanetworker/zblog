---
title:  "DOCKument: Auto-generating Documentation for Dockerfiles and Images"
date:   2018-03-19 15:04:23
categories: [technical]
tags: [Docker, Containers]
---


DOCKument is a tool that helps you auto-generate documentation for your Dockerfiles and Docker images the way you would an API.

Matthias Lübken gave an example on how to use "API" like labels to describe important information in the Dockerfile (e.g., resources used, exposed ports, dependencies, etc) [here](https://github.com/luebken/currentweather/blob/master/Dockerfile). This project makes use of similar label patterns to fetch important data in Dockerfiles / Docker images and create Dockumentation for it automagically. Furthermore, it improves on the work done by Matthias to expose various kinds of information that can be fetched from Docker images.

In this post we will go through the usecase, usage, pre-reqs, getting started, and a little teaser for the next post.

<!-- TOC -->

- [Usecases](#usecases)
- [How to Use it](#how-to-use-it)
- [Pre-reqs](#pre-reqs)
- [Getting Started](#getting-started)
- [Next Post](#next-post)

<!-- /TOC -->

### Usecases

Dockument on its own is just a tool that gives you the ability to fetch information out of Dockerfiles or built images. You can choose to create a Markdown output of your Dockerfile  and Docker image or you can cherry pick on what to fetch from the image and visualize it's content. Although this is not the main strength point of Dockument, it can come in handy when trying to share your Dockerfile on a public repo, it instantly allows the readers to understand the highlights of your Dockerfile / image (e.g., resources exposed, environment variables, dependencies, tests, and much more).

The use-case above reflects the usage of Dockument only as a **tool**. What we can also do with Dockument, which I personally consider to be the main strength point, is using it as a framework for fetching important information out Docker images. If used in this manner, other tools can use this information to provide useful utilities as will be described in my next post about `Docktorino` the contious testing tool for Docker images. 


### How to Use it

With DOCKument you have the option to fetch important information about your Dockerfile or Docker images direcly from the command line **OR** create a markdown document that you can add to your git REPO or share with other developers.

For example, if you type `dockument deps -d "../examples/Dockerfile"`, you will get the following output which describes the dependencies of this container image:

```bash
### Dependency RABBIT ###
	Application: rabbit
	Image: "rabbit:latest"
	Description: "The rabbit"
	Ports: ["5271"]
	Required: "true"

### Dependency REDIS ###
	Application: redis
	Image: "redis:latest"
	Description: "For caching results from OWM API."
	Ports: ["6379"]
	Required: "true"
``` 

On the other hand, to create a markdown DOCKument, you can use `dockument create --dockerfile "../examples/Dockerfile" -o "$HOME/dockumentation.md"` or `dockument create --image "repo/name:tag" -o "$HOME/dockumentation.md"` . The output will be a file similar to the one [here](https://github.com/zanetworker/dockument/blob/master/examples/dockumentation.md)


### Pre-reqs

There are two types of labels that `Dockument` supports, these are: 

- **Misc Lables**: these are random / non-standard  labels defined in your Docker image, basically any label that does not follow the pattern described below.
- **Standardized Labels**: These are the gems of `Dockument`, these labels are the ones used to expose the important information about your Dockerfile / Docker image. They follow a pattern, always prefiexed with **api.Type.***

You can use `Dockument` to print or create a documentation of random metadata in your Dockerfile or Docker images, however, that is not very useful. To utilize the fully-fledged functionality of Dockument, define labels in your Docker images that follow the below pattern.

**For Dependencies**:

Will this containerized application have dependencies on other applications?

```dockerfile
LABEL api.DEPENDENCY.redis=""\
      api.DEPENDENCY.redis.image="redis:latest"\
      api.DEPENDENCY.redis.port="6379"\
      api.DEPENDENCY.redis.about="For caching results from OWM API."\
      api.DEPENDENCY.redis.mandatory="true"
```

**For Important ENVs**:

What are the environment variables that this container image defines? 

```dockerfile
LABEL api.ENV.OPENWEATHERMAP_APIKEY="" \
      api.ENV.OPENWEATHERMAP_APIKEY.about="Access key for OpenWeatherMap. See http://openweathermap.org/appid for details." \
      api.ENV.OPENWEATHERMAP_APIKEY.mandatory="true"
```

**For Exposed Ports**:

Do we expose any ports? 

```dockerfile
LABEL api.EXPOSE.1337="" \
      api.EXPOSE.1337.scheme="tcp" \
	api.EXPOSE.1337.protocol="http"\
      api.EXPOSE.1337.about="The main endpoint of this service."
```

**For resources used by the container image**:

What resources are required by my application to run in a smooth and performant manner?

```dockerfile
LABEL api.RESOURCES.Memory="3gb"\
      api.RESOURCES.CPU="2"
```

**For Important Tags** 

What are some important tags that I want my Dockerfile to expose?

```dockerfile
LABEL api.TAGS.go="1.9"
```

**For Container Tests** 

Tests help you make sure that your container image conforms with what you actually wants it to do. To this end, we decided to support the [container-structured-test](https://github.com/GoogleCloudPlatform/container-structure-test) style. There are four types that are supported by Dockument:

- **Command Tests:** "Command tests ensure that certain commands run properly in the target image".


```dockerfile
LABEL api.TEST.command=""\
      api.TEST.command.name="go version"\
      api.TEST.command.command="go"\
      api.TEST.command.args="version"\
      api.TEST.command.expectedOutput="go version"
```

- **File Existence Tests:** "File existence tests check to make sure a specific file (or directory) exist within the file system of the image."

```dockerfile
LABEL api.TEST.fileExistence=""\
      api.TEST.fileExistence.name="Dockumentation Check"\
      api.TEST.fileExistence.path="/dockumentation.md"\
      api.TEST.fileExistence.shouldExist="true"\
      api.TEST.fileExistence.permissions=""
```

- **File Content Tests:** "File content tests open a file on the file system and check its contents." 

```dockerfile
LABEL api.TEST.fileContent=""\
      api.TEST.fileContent.name="Debian Sources"\
      api.TEST.fileContent.path="/etc/apt/sources.list"\
      api.TEST.fileContent.expectedContents="['.*httpredir\\.debian\\.org.*']"\
      api.TEST.fileContent.excludedContents="['.*gce_debian_mirror.*']"
``` 
- **Meta data tests**: "The Metadata test ensures the container is configured correctly". 

```dockerfile
LABEL api.TEST.metadata=""\
      api.TEST.metadata.env="GOPATH:/go"\
      api.TEST.metadata.exposedPorts=""\
      api.TEST.metadata.volumes=""\
      api.TEST.metadata.cmd=""\
      api.TEST.metadata.workdir=""
```


By now, I am guessing you might have an idea of how labels are defined and what Dockument does, but you are missing a little demo to get the gist of it, so lets do just that.

<script src="https://asciinema.org/a/pdVCkPpvz7OAdjYpuOz4gMY1j.js" id="asciicast-pdVCkPpvz7OAdjYpuOz4gMY1j" async></script>

For a complete example, please have a look at [this Dockerfile](https://github.com/zanetworker/dockument/blob/master/examples/Dockerfile) or [this Dockerfile by luebken](https://github.com/luebken/currentweather/blob/master/Dockerfile)

### Getting Started

To get started, visit the [github repo for DOCKument](https://github.com/zanetworker/dockument) and download the corresponding binaries, or build it yourself :)

### Next Post

As mentioned earlier in this post, Dockument can be either used as a tool or as a framework that other tools can capitalize on. In the next post, we will be taking about `Docktorino`, a real-time continious testing tool for your docker builds that will help you containerize with confidence!
