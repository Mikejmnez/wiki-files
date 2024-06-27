## Overview

This document describes how to make a release of the official Hyrax
Docker image

Each of our official Docker image builds are based on an associated
official release of the Hyrax server. The images are built using the
official release tar/war files whose authenticity is verified during the
build.

In our Docker project, each release of Hyrax has its own directory.
Making a new release begins with copying the template files to a new
release directory, editing their contents, and of course testing that it
works.

## Automated Build (hyrax-xx.yy.zz docker containers)

This build will utilize version numbers to grab the components of Hyrax
from www.opendap.org/pub and build the docker container with them.

### Create And Build Release Images

From the command line make the working directory the top of the
**hyrax-docker** project.

#### Update the mkDist script

Edit the **mkDist** file and update the values at the top of the file to
reflect the various release components and the release date.

Example:

    HYRAX_MAJOR_VERSION="1.15"
    HYRAX_FULL_VERSION="1.15.3"
    OLFS_VERSION="1.18.3"
    LIBDAP_VERSION="3.20.3-1"
    BES_VERSION="3.20.3-1"
    RELEASE_DATE="2019-02-25"

Save the *mkDist* file.

#### Run the mkDist script


`./mkDist`

At this point, unless errors were encountered, all the images should be
built.

### Test the images.

They should already be tagged with the various names, including the
images that will be needed for docker hub:


**opendap/hyrax:xx.yy.zz**

**opendap/hyrax:latest**

**opendap/hyrax_ncwms:xx.yy.zz**

**opendap/hyrax_ncwms:latest**

Test the Hyrax image like this:


`docker run -d -h hyrax -p 8080:8080 --name=hyrax opendap/hyrax:latest`

Test the Hyrax with ncWMS image like this:


`docker run -d -h hyrax -p 8080:8080 --name=hyrax_ncwms opendap/hyrax_ncwms:latest -n `[`http://localhost:8080`](http://localhost:8080)

And to see the ncWMS working look here:


[`http://localhost:8080/ncWMS2/Godiva3.html?server=http://localhost:8080/ncWMS2/wms/lds/data/nc/coads_climatology.nc`](http://localhost:8080/ncWMS2/Godiva3.html?server=http://localhost:8080/ncWMS2/wms/lds/data/nc/coads_climatology.nc)

### Upload the tested images to DockerHub

To get them into docker hub:


`docker push opendap/hyrax:xx.yy.zz`

`docker push opendap/hyrax:latest`

`docker push opendap/hyrax_ncwms:xx.yy.zz`

`docker push opendap/hyrax_ncwms:latest`

### Update The DockerHub Landing Pages

In DockerHub each of our published Docker images has a landing page. The
landing pages for the hyrax and the hyrax_ncwms images contain
exaplanation and a list of links to official release version. These need
to be updated.

To update:

1.  Log into DockerHub
2.  Navigate to <https://hub.docker.com/repository/docker/opendap/hyrax>
    1.  Add the new release to the list of Release and Docker files.
    2.  Update the link to the ''Latest' Docker file.
3.  Navigate to
    <https://hub.docker.com/repository/docker/opendap/hyrax_ncwms>
    1.  Add the new release to the list of Release and Docker files.
    2.  Update the link to the ''Latest' Docker file.

### Update Documention

In GitHub, update the **hyrax-docker/README.md** file with the new
release numbers and associated DOI badges and verify that the
instructions continue to reflect the current build.

### Tag the Release

1.  Make sure all changes have been pushed and merged to the master
    branch.
2.  Since the Docker project is essentially linked to a Hyrax Release,
    tag this point in the master branch with the Hyrax release number
    - `git tag -a hyrax-`<numbers>` -m "Hyrax `<number>`"`
    - `git push origin hyrax-`<numbers>

------------------------------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

------------------------------------------------------------------------

## Manually build the Hyrax release docker container

Follow the basic instructions to build the docker containers in the
[github project's README](https://github.com/OPENDAP/hyrax-docker).

### Overview: Building For Distribution

Here are the basics steps:

- Clone the repo
- Copy the *hyrax-latest* dir to the next version number if needed and
  re-assign the symbolic link
- cd into the directory and build the *hyrax* container both with and
  without ncWMS *It is easiest todo all the steps for the plain image,
  then repeat for the image with ncWMS*
- Build the image
- Test the image by running it.
- Stop the running container (Running image is called a container)
- Create an image from the stopped container (via the container_id)
  using the name of the target repository_id and `docker commit`
- Login to Docker.
- Tag the image.
- Push the image.

#### Cheat sheet

Run an interactive shell in a container
To grab a container and run a shell, use *docker run -it --name=builder
opendap/centos7_hyrax_builder:1.1 /bin/bash* where *-it* is the magic
option.

Update the state of a container
This includes adding new packages to an existing container

- First run an interactive shell in the container: *docker run -it
  --name=builder opendap/centos7_hyrax_builder:1.1 /bin/bash*
- Run commands in the container's shell to do whatever needs to be done
  to add/remove/configure the container. For example, to add the *aws
  cli* tools, I ran *yum install epel-release*, *yum install
  python-pip*, *pip install --upgrade pip*, and *pip install --user
  awscli*
- Then, exist from the shell back to the controlling terminal. The
  'container' (*builder* in the above) will be stopped once you exit
  (using ctrl-d).
- Commit the container: *docker commit builder
  opendap/centos6_hyrax_builder:1.2* where *centos6_hyrax_builder* is
  the basename of the container from the Docker Hub repo in the first
  step
- Push the updated and committed container: *docker push
  opendap/centos6_hyrax_builder:1.2*

Run an interactive shell in a container
To grab a container and run a shell, use *docker run -it --name=builder
opendap/centos7_hyrax_builder:1.1 /bin/bash* where *-it* is the magic
option.

<!-- -->

Update the state of a container
(This includes adding new packages to an existing container)

- First run an interactive shell in the container: *docker run -it
  --name=builder opendap/centos7_hyrax_builder:1.1 /bin/bash*
- Run commands in the container's shell to do whatever needs to be done
  to add/remove/configure the container. For example, to add the *aws
  cli* tools, I ran *yum install epel-release*, *yum install
  python-pip*, *pip install --upgrade pip*, and *pip install --user
  awscli*
- Then, exist from the shell back to the controlling terminal. The
  'container' (*builder* in the above) will be stopped once you exit
  (using ctrl-d).
- Commit the container: *docker commit builder
  opendap/centos6_hyrax_builder:1.2* where *centos6_hyrax_builder* is
  the basename of the container from the Docker Hub repo in the first
  step
- Push the updated and committed container: *docker push
  opendap/centos6_hyrax_builder:1.2*

### Build and push the hyrax image

- Build the image



`docker build -t hyrax_image hyrax`

- Run the image like this



`docker run -h hyrax -p 8080:8080 --name=hyrax_container hyrax_image`

And have a look at <http://localhost:8080/opendap> You should see Hyrax
running in Docker<img src="Screenshot_2018-12-08_10.09.41.png"
title="Screenshot_2018-12-08_10.09.41.png" width="200"
alt="Screenshot_2018-12-08_10.09.41.png" />

- Find the 'container ID' for the docker image. If you used the `--name`
  parameter when you invoked `docker run` above then that's the
  container_id, otherwise you can find it using



`docker ps`

For this example it's `hyrax_container`

- Stop the running container



`docker container stop hyrax_container`

- Use `docker commit` to create a repository image of the container
  using the container_id and its target repository name. (In this
  example we use `hyrax_container` for the container_id and `hyrax` as
  the repository name.)



`docker commit hyrax_container hyrax`

- Login to the OPeNDAP docker hub account using an account name linked
  to the opendap repo on the Docker Hub main site



`docker login`

- Tag and Push the repository image. Do this two times, first using the
  tag '`opendap/hyrax:`<version>' and then '`opendap/hyrax:latest`'. The
  string format for the tags for the docker container/image are
  structured like this,



***<reponame>**/**<repo_id>**:**<tag>***

Where:

- *<reponame>* is the *opendap* repo name (this is really the opendap
  section of the main Docker Hub repository),
- *<repo_id>* is the commit name from the *docker commit ...* command
  above
- *<tag>* is version (for example 1.15.2) or the string 'latest'.

Once tagged, push the container up to the repo using *docker push
<tagged name>*


`docker tag `*`hyrax_container opendap/hyrax:x.y.z`*

`docker push `*`opendap/hyrax:x.y.z`*

`docker tag `*`hyrax_container opendap/hyrax:latest`*

`docker push `*`opendap/hyrax:latest`*

### Build and push the hyrax_ncwms image

- Build the image



`docker build -t hyrax_ncwms_image --build-arg USE_NCWMS=true hyrax`

- Run the image like this



`docker run -h hyrax -p 8080:8080 --name=hyrax_ncwms_container hyrax_ncwms_image -n `[`http://localhost:8080`](http://localhost:8080)

And then have a look at <http://localhost:8080/opendap> You should see
Hyrax running in Docker<img src="Screenshot_2018-12-08_10.09.41.png"
title="Screenshot_2018-12-08_10.09.41.png" width="200"
alt="Screenshot_2018-12-08_10.09.41.png" />

> ***Note:** The value of the `-n` parameter at the end of the
> `docker run` command should be the outward facing IP/domain_name of
> your docker container's tomcat endpoint. If you are building and
> running this on your local system then <http://localhost:8080> will
> probably work great. If you are deploying the Hyrax container in AWS
> then you'll have to do more work to get that value.*

- Find the 'container ID' for the docker image. If you used the `--name`
  parameter when you invoked `docker run` above then that will work,
  otherwise you can find it using



`docker ps`

For this example it's `hyrax_ncwms_container`

- Stop the running container



`docker container stop hyrax_ncwms_container`

- Use `docker commit` to create a repository image of the container
  using the container_id and its target repository name. (In this
  example we use `hyrax_ncwms_container` for the container_id and
  `hyrax_ncwms` as the repository name.)



`docker commit hyrax_ncwms_container hyrax_ncwms`

- Login



`docker login`

- Tag and Push the repository image.



`docker tag `*`hyrax_ncwms opendap/hyrax_ncwms:x.y.z`*

`docker push `*`opendap/hyrax_ncwms:x.y.z`*

`docker tag `*`hyrax_ncwms opendap/hyrax_ncwms:latest`*

`docker push `*`opendap/hyrax_ncwms:latest`*

### Docker command info

For the tag command: [Docker Tag](https://docs.docker.com/engine/reference/commandline/tag/)
For the push command (this shows the commit, tag, push sequence as an example): [Docker Push](https://docs.docker.com/engine/reference/commandline/push/#parent-command)