# Command Central Docker Builder

This project demonstrates how to build docker images using
Command Central Docker Builder.

## Overview

Command Central Docker Builder is an tool provided by Software AG
on [Docker Store](https://store.docker.com/images/softwareag-commandcentral). It leverages Command Central templates to run
provisioning operations during Docker image build, driven from a standard Dockerfile.

## Configuration

Login to Docker Store with your Docker ID, open https://store.docker.com/images/softwareag-commandcentral and accept license agreement to get access
to Command Central images.

Login to Docker with your Docker ID from your console and verify you can download the images:

```bash
docker login
docker pull store/softwareag/commandcentral:10.1.0.1-server
```

Modify [init.yaml](init.yaml) to configure product and fix repositories, poiting to Master 10.1 repostories on Empower using your Empower credentials or point to your local 10.1 mirrors

```yaml
  product:
    products:
      location: http://sdc.softwareag.com/dataservewebM101/repository/
      username: you@company.com
      password: yourpass
  fix:
    fixes:
      location: http://sdc.softwareag.com/updates/prodRepo
      username: you@company.com
      password: yourpass
```

Add your 10.1 webMethods Microservices Container license file to the current folder as ```licenseKey.xml``` file.

## Building images

To build images using Command Central composite template run

```bash
docker-compose build
```

See [template.yaml](template.yaml) for basic webMethods Microservices Container (MSC) runtime instance definition.

There are 3 flavors of images:

* [Simple](Dockerfile.simple) - easy way to build images
* [Unmanaged](Dockerfile.unmanaged) - customized image without SPM management agent
* [Managed](Dockerfile.managed) - customized image with SPM management agent

Check image sizes:

```bash
docker images
```

## Running containers

Start Command Central container first

```bash
docker-compose run --rm init
```

Start all 3 types of containers we just built:

```bash
docker-compose up -d
```

Wait up to 2 minutes and access them as:

* Simple - [http://0.0.0.0:5551/](http://0.0.0.0:5551/)
* Unmanaged - [http://0.0.0.0:5552/](http://0.0.0.0:5552/)
* Managed - [http://0.0.0.0:5553/](http://0.0.0.0:5553/)

Login as Administrator/manage

## Simple image

There is no size optimization for this image but it is the easiest to build.
Suitable for ad-hoc testing.

Simple image comes with SPM and thus can be managed in Command Central.

## Unmanaged image

Unmanaged image does not include management agent (SPM) and thus cannot be managed
by Command Central, but can be managed by an orchestration tool of your choice.

## Managed image

Managed image includes Command Central agent (SPM) and thus:

* You can monitor, administer and even configure it in Command Central
* And still manage by an orchestration tool of your choice

Open [Command Central](https://0.0.0.0:8091/) to see simple and managed containers in
the maganed landscape.

You can run tests against them using Command Central API:

```bash
docker-compose run --rm test
```

Tail the logs if you need to

```bash
docker-compose logs -f
```

## Cleanup

```bash
docker-compose down
```

## Adoping templates to work with Command Central Docker Builder 10.1

> Command Central Docker Builder 10.1 has the following requirements for the template.yaml:

* The template alias MUST be *container*, e.g. `alias: container`
* The template MUST use repositories named *products* and *fixes* to match those defined in `init.yaml` file
* There MUST be no required parameters in the template. All parameters MUST have default values
* The template MUST provision the layer(s) to the `local` node alias

See the difference between [adopted template.yaml](template.yaml) and [original template.yaml](https://github.com/SoftwareAG/sagdevops-templates/blob/master/templates/sag-msc-server/template.yaml) for MSC basic template.

> The above limitations are planned to be removed in the upcoming Command Central Builder release to allow seamless use of the same templates for VMs and containers

## Troubleshooting

### Running on Windows host

Your Docker build runs on Windows and fails, ensure that git client uses UNIX crlf instead of Windows.
Change git configuration and re-clone the repository

```bash
git config --global core.autocrlf false
```

_______________
Contact us at [TECHcommunity](mailto:technologycommunity@softwareag.com?subject=Github/SoftwareAG) if you have any questions.
_______________
DISCLAIMER
These tools are provided as-is and without warranty or support. They do not constitute part of the Software AG product suite. Users are free to use, fork and modify them, subject to the license agreement. While Software AG welcomes contributions, we cannot guarantee to include every contribution in the master project.
