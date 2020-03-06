---
title:  "Docktorino: Building Containers with Confidence"
date:   2018-04-01 15:04:23
categories: [technical]
tags: [Docker, Containers, Testing]
---

Docktorino is a real-time continious testing tool for your docker builds, helping you containerize with confidence!

When building Docker images it is sometimes tricky to assert the image behavior, for example, whether the contents of the image you built is correct, or that commands can execute correctly inside your container ( maybe you forgot to set your binary's PATH).

Docktrino makes these types of assertions easy to define when building your Docker images. It then listens to your Docker builds and triggers these tests on the spot to notify you if you have done something wrong, or if your container image is misbehaving.


The outline for this post is as follows: 
<!-- TOC -->autoauto- [Step Stones](#step-stones)auto- [Usecases](#usecases)auto- [Demo](#demo)auto- [Getting Started](#getting-started)autoauto<!-- /TOC -->

### Step Stones

In the previous post, I talked about [**Dockument**](https://github.com/zanetworker/dockument), a tool that helps you auto-generate documentation for your Dockerfiles and Docker images the way you would an API. Docktorino  *uses Dockument as a library to fetch important data from Docker images*. 

Furthermore, it uses the fetched information, in particular **testing** information which follows the [**container-structure-test**](https://github.com/GoogleCloudPlatform/container-structure-test) style as previously explained in the [Dockument blog post](http://www.adelzaalouk.me/2018/dockument/) and produces testing results triggered by a build to the target image. The ouput again conforms with the `container-structure-test` output. 


### Usecases

```dockerfile
FROM golang:alpine as builder

LABEL api.TEST.fileExistence=""\
      api.TEST.fileExistence.name="Dockumentation Check"\
      api.TEST.fileExistence.path="/dockumentation.md"\
      api.TEST.fileExistence.shouldExist="true"\
      api.TEST.fileExistence.permissions=""
ADD dockumentation.md /dockumentation.md

LABEL api.TEST.command=""\
      api.TEST.command.name="go version"\
      api.TEST.command.command="go"\
      api.TEST.command.args="version"\
      api.TEST.command.expectedOutput="go version"
      
LABEL api.TEST.metadata=""\
      api.TEST.metadata.env="GOPATH=/go,PATH=/go/bin:/usr/local/go/bin:$PATH"\
      api.TEST.metadata.exposedPorts=""\
      api.TEST.metadata.volumes=""\
      api.TEST.metadata.cmd=""\
      api.TEST.metadata.workdir=""
ENV PATH /go/bin:/usr/local/go/bin:$PATH
ENV GOPATH /go
```


Consider the above Dockerfile, it defines three tests to assert the behavior of your Docker image: 
- Tests the the file `dockumentation.md` that we add exists in the image (as intended) via a `fileExistence test` 
- Tests that go is successufully installed via a `command test` 
- Tests that the environment variables we defined (PATH and GOPATH) have the right values. 

We can also tests the content of a file via `fileContent tests`. For example, if we are to build a debian image, we can check the `sources.list` file contents as follows: 

```dockerfile
LABEL api.TEST.fileContent=""\
      api.TEST.fileContent.name="Debian Sources"\
      api.TEST.fileContent.path="/etc/apt/sources.list"\
      api.TEST.fileContent.expectedContents="['.*httpredir\\.debian\\.org.*']"\
      api.TEST.fileContent.excludedContents="['.*gce_debian_mirror.*']"
``` 

**Important To Note**

Multiple environment variables can be checked as shown in the meta-data tests, all what we need to do is separate env definitions with a "," (e.g., `api.TEST.metadata.env="GOPATH=/go,PATH=/go/bin:/usr/local/go/bin:$PATH"`), same goes for ARGs in our command tests, we can have multiple args to the command tests, we just need to add a "," to separate the args.


### Demo 

<iframe width="560" height="315" src="https://www.youtube.com/embed/lU7hpP2nfPw" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>


### Getting Started
To get started, please visit the git repo of Docktorino [here](https://github.com/zanetworker/docktorino), and download the corresponding binaries, or build it yourself.

