Source-2-Image for jitsi-meet
====================

This repository contains the source for building Node.JS, nginx and jitsi-meet as a reproducible Docker image using [source-to-image](https://github.com/openshift/source-to-image).
Users can choose between RHEL and CentOS based builder images.
The resulting image can be run using [Docker](http://docker.io).

For more information about using these images with OpenShift, please see the
official [OpenShift Documentation](https://docs.openshift.org/latest/using_images/s2i_images/nodejs.html).


Versions
---------------
Node.JS version currently provided is **NodeJS 6**

nginx version currently provided is **nginx 1.12**

jitsi-meet version currently provided is 

RHEL versions currently supported are:
* RHEL7

CentOS versions currently supported are:
* CentOS7


Installation
---------------
To build a jitsi-meet image, choose either the CentOS or RHEL based image:
*  **RHEL based image**

    This image is available in Red Hat Container Registry. To download it run:

    ```
    $ docker pull styke/jitsi-meet-rhel7
    ```

    To build a RHEL based Node.JS image, you need to run the build on a properly
    subscribed RHEL machine.

    ```bash
    $ git clone --recursive https://github.com/styke-io/s2i-jitsi-meet.git
    $ cd s2i-jitsi-meet
    $ git submodule update --init
    $ make build TARGET=rhel7
    ```

*  **CentOS based image**

    This image is available on DockerHub. To download it run:

    ```bash
    $ docker pull styke/jitsi-meet-centos7
    ```

    To build a image from scratch run:

    ```bash
    $ git clone --recursive https://github.com/styke-io/s2i-jitsi-meet.git
    $ cd s2i-jitsi-meet
    $ git submodule update --init
    $ make build TARGET=centos7
    ```


Usage
---------------------------------

For information about usage of Dockerfile for NodeJS 6,
see [usage documentation](6/README.md).
For information about usage of Dockerfile for NodeJS 8,
see [usage documentation](8/README.md).

Test
---------------------
This repository also provides a [S2I](https://github.com/openshift/source-to-image) test framework,
which launches tests to check functionality of a simple Node.JS application built on top of the s2i-nodejs image.

Users can choose between testing a Node.JS test application based on a RHEL or CentOS image.

*  **RHEL based image**

    To test a RHEL7 based Node.JS image, you need to run the test on a properly
    subscribed RHEL machine.

    ```
    $ cd s2i-jitsi-meet
    $ git submodule update --init
    $ make test TARGET=rhel7
    ```

*  **CentOS based image**

    ```
    $ cd s2i-jitsi-meet
    $ git submodule update --init
    $ make test TARGET=centos7
    ```


Repository organization
------------------------
* **`6`**

    * **Dockerfile**

        CentOS based Dockerfile.

    * **Dockerfile.rhel7**

        RHEL based Dockerfile. In order to perform build or test actions on this
        Dockerfile you need to run the action on a properly subscribed RHEL machine.

    * **`s2i/bin/`**

        This folder contains scripts that are run by [S2I](https://github.com/openshift/source-to-image):

        *   **assemble**

            Used to install the sources into the location where the application
            will be run and prepare the application for deployment (eg. installing
            modules using npm, etc.)

        *   **run**

            This script is responsible for running the application, by using the
            application web server.

        *   **usage***

            This script prints the usage of this image.

    * **`contrib/`**

        This folder contains a file with commonly used modules.

    * **`test/`**

        This folder contains the [S2I](https://github.com/openshift/source-to-image)
        test framework with simple Node.JS echo server.

        * **`test-app/`**

            A simple Node.JS echo server used for testing purposes by the [S2I](https://github.com/openshift/source-to-image) test framework.

        * **run**

            This script runs the [S2I](https://github.com/openshift/source-to-image) test framework.
