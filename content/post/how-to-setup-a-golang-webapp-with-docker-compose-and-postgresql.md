+++
date = 2021-06-15T11:20:00Z
description = "A simple way to setup a Golang API with Docker Compoes and Postgresql"
draft = true
image = "/uploads/external-content-duckduckgo-com.png"
tags = ["Postgresql", "Docker", "Docker Compose", "Golang"]
title = "Develop a Golang webapp with Docker Compose and Postgresql"

+++
## Prerequisites

Before getting started, this tutorial assumes that one has at least basic knowledge of Golang, enough at least to read the code. The use of docker compose is to be able to at least add a database and ensure the code can be run on any device without having to have the tools installed on the machine.

The following definition below, got from the docker compose website, gives a rough overview of what it is and why it is advisable to use it.

> Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration

## Installation

You will require to install the following tools:

* [Golang](https://golang.org/dl/)
* [Docker](https://docs.docker.com/get-docker/)
* [Docker Compose](https://docs.docker.com/compose/install/)

## The Golang application

We will write a simple Golang application web application in order to test out that the connection works since the main point of the blog is to explore docker compose and deploying to production.