---
title:  "Demystifying Moby: From OCI to Linuxkit"
date:   2017-05-03 15:04:23
categories: [technical]
tags: [Docker, Microservices, Moby, Linuxkit]
---

OCI, Containerd, runC, Moby, linuxkit are all relatively new components that work together for building containers.
In this post we will explain each component and how these components relate to one another.


- [Lets Start with the Basics (OCI, runC, Containerd)](#lets-start-with-the-basics-oci-runc-containerd)
  - [So what is the Open Container Initiative (OCI)?](#so-what-is-the-open-container-initiative-oci)
- [What are runC and Containerd?](#what-are-runc-and-containerd)
- [Demistyfing Moby & The LinuxKit](#demistyfing-moby--the-linuxkit)
- [Linuxkit Test-Drive](#linuxkit-test-drive)
- [Important References](#important-references)

## Lets Start with the Basics (OCI, runC, Containerd)

Before jumping in and talking about Moby and Linuxkit, it is important to establish some basic
knowledge first, this will make understanding Linuxkit much easier.

### So what is the Open Container Initiative (OCI)?

The OCI is an initiative to define common standards for container frameworks to build containers. Container engines such as _Docker_ and _rkt_ provided an easy way to run containers by just providing the container image name and the version, for example:

```bash
docker run repo_name/image_name:tag_name
rkt run rep_name/image_name,version=version_tag

```

To keep supporting this easy way of building containers, OCI needs to define an image format that provide information such as (commands, env variables, arguments, etc) for the container to use later and so they did, so now we have two specifications by OCI:
- **Image Format Specification**: Contains the image manifest, file-system layers, and the image configuration (similar to how you do it user a Dockerfile)
```json
{
  "created":"2015-10-31",
  "author":"",
  "architecture":"amd64",
  "os":"linux",
  "config":{
    "User":"alice",
    "ExposedPorts":{  },
    "Env":[  ],
    "Entrypoint":[
      "/bin/my-app-binary"
    ],
    "Cmd":[  ],
    "Volumes":{  },
    "WorkingDir":"/home/alice",
    "Labels":{  }
  },
  "rootfs":{  },
  "history":[  ]
}
```
- **Runtime Specification**: specified the configuration, execution environment, PID, and lifecycle of a container (e.g., running, created, stopped, etc). Below is an example of runtime and configuration:
```json
{
  "ociVersion":"1.0.0-rc5-dev",
  "platform":{
    "os":"linux",
    "arch":"amd64"
  },
  "process":{  },
  "root":{
    "path":"rootfs"
  },
  "mounts":[
    {
      "destination":"/sys",
      "type":"sysfs",
      "source":"sysfs",
      "options":[
        "nosuid",
        "noexec",
        "nodev"
      ]
    }
  ],
  "linux":{
    "resources":{
      "disableOOMKiller":false
    },
    "namespaces":[
      {
        "type":"pid"
      },
      {
        "type":"ipc"
      },
      {
        "type":"uts"
      },
      {
        "type":"mount"
      }
    ]
  }
}
```

With OCI, we will have a bundle that contains configuration files for the runtime to act upon.  I hope this gave you a better idea on what OCI is.  Time to move to runC and Containerd.

## What are runC and Containerd?
Now we have a standard that dictates how to run containers, and a bundle with configuration, so how will we use this standard to run them now. lets **runC**.

runC is a container runtime that knows how to deal with OCI defined speicifcations. To use runC you need to have a ```rootfs``` and an OCI ```config.json``` which can be created using ```runc spec```.

Now that we have all what we need, we can start a container:
```bash
runC run container-name # runs the container
runC list # to see the containers
```

Alright, so now we have a runtime for containers that understands and talks OCI, yay! are we done now? well, you can stop here and go run containers with ```runC```, but I might not be interested in using runC in the future, so I will go learn how to use ```Containerd```, this way even if I want to use another container runtime (that supports OCI), I will not have to understand how this runtime works, I will instead rely on Containerd.

**Containerd** is daemon to manage the complete container lifecycle, it abstracts **runC** details and provides a **gRPC** compataible API that can be used natively or from a command terminal (ctr).

Lets have a look at the entire picture so far!
![](/assets/linuxkit_post/containerd.png)


Looks good right? So now that we are armed with this knowledge lets free Moby!


## Demistyfing Moby & The LinuxKit

So recently a project called Moby was announced at Dockercon, the announcement raised alot of concerns about how the current docker framework will be used compared to Moby. But worry not, there is no need for confusion.

Based on my understanding, Moby is a project that gathers together all the tools used internally by the docker teams and makes it public for developers and contributers to share a common repository to innovate. It basically contains all the lego pieces needed to build containers, swarms, etc. Moby is thought of as an assembly factory, you can create an assemly using different parts and you have a product at the end. Another analogy is a juice mixer, you put all the fruits you want in the mixer and you get a nice smoothie (yumm).


![](/assets/linuxkit_post/MobyProject.PNG)

One of major announcements in Dockercon was Linuxkit, which falls under the Moby Project as a core component for building minimal, customizable, user-case oriented images that can run on many platforms, with isolation as a bonus side-effect.

![](/assets/linuxkit_post//linuxkit.png)

## Linuxkit Test-Drive
Alright, lets put this knowledge into practice. Here is what you will need to get a bootable linuxkit customizable image at the end:
- Clone the [linuxkit repository](https://github.com/linuxkit/linuxkit)
- Inside the linuxkit repo run ```make clean && make && make install``` or you can run ```make``` and copy all the contents of bin/* to your PATH

Now you have moby (the tool for building images), and linuxkit for deploying them. So how do you define your images? we use a .yml config file. Lets have a look at the important bits of the yaml file for building a docker linuxkit image (which you can find in the ```examples/``` directory):

```yaml

kernel:
  #This section defines the kernel configuration,  The image field specifies the Docker image, which should contain a bzImage (for amd64 architecture, others may vary) and a file called kernel.tar
  image: "linuxkit/kernel:4.9.x"
  cmdline: "console=ttyS0 console=tty0 page_poison=1"
```
![](/assets/linuxkit_post/extracting.PNG)

```yaml
init:
  #This section currently just lists images that is used for the init system and are unpacked directly into the root filesystem
  - linuxkit/init:63eed9ca7a09d2ce4c0c5e7238ac005fa44f564b
  - linuxkit/runc:b0fb122e10dbb7e4e45115177a61a3f8d68c19a9
  - linuxkit/containerd:18eaf72f3f4f9a9f29ca1951f66df701f873060b
  - linuxkit/ca-certificates:e091a05fbf7c5e16f18b23602febd45dd690ba2f
```
![](/assets/linuxkit_post/init_containers.PNG)

```yaml
onboot:
  #These containers are run to completion sequentially, using runc before anything else is started
  - name: sysctl
    image: "linuxkit/sysctl:1f5ec5d5e6f7a7a1b3d2ff9dd9e36fd6fb14756a"
    net: host
    pid: host
    ipc: host
    capabilities:
     - CAP_SYS_ADMIN
    readonly: true
...
```
![](/assets/linuxkit_post/onboot_containers.PNG)

```yaml
services:
  #These containers are started with containerd and are expected to remain running
  - name: docker
    image: "linuxkit/docker-ce:a82b61b151f6d6a3606e66f284c927b4c83b50f5"
    capabilities:
     - all
    net: host
    mounts:
     - type: cgroup
       options: ["rw","nosuid","noexec","nodev","relatime"]
    binds:
     - /var/lib/docker:/var/lib/docker
     - /lib/modules:/lib/modules
...
```
![](/assets/linuxkit_post/service_containers.PNG)

```yaml
files:
  - path: etc/docker/daemon.json
    contents: '{"debug": true}'
```
![](/assets/linuxkit_post/add_files.PNG)

```yaml
trust:
  #his section specifies which build components are to be cryptographically verified with Docker Content Trust prior to pulling
  image:
    - linuxkit/kernel
    - linuxkit/binfmt
    - linuxkit/rngd
outputs:
  - format: kernel+initrd
```
![](/assets/linuxkit_post/Create_Output.PNG)


To build the linuxkit image we use ```moby``` the tool:
```bash
moby build docker.yml
```
Finally to deploy the image, use:
```bash
  linuxkit run docker
```

The end result should look like this:

![](/assets/linuxkit_post/docker_linux_lit.PNG)

As you can see, docker running as a runc container inside the linuxkit image.

## Important References
- [OCI Runtime-Spec](https://github.com/opencontainers/runtime-spec/blob/master/config.md)
- [OCI Image-Spec](https://github.com/opencontainers/image-spec/blob/master/spec.md)
- [runc.io](https://runc.io/)
- [ Containerd to Control runc](https://blog.docker.com/2015/12/containerd-daemon-to-control-runc/)
- [containerd.io](https://containerd.io)
