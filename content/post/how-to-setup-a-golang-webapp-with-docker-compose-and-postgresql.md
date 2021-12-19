+++
date = 2021-12-14T20:20:00Z
description = "A simple way to setup a Golang API with Docker Compoes and Postgresql"
image = "boy.jpg"
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

First thing first, let's show a simple file structure
```
      +-- docker-compose.yml
      +-- main.go
      +-- Dockerfile
```
Pretty simple right? Let's get started:

We'll create a very simple Golang application that has a hello World route and connects to the database, since the main aim of this tutorial is to dockerize our web application and not to create some fancy web app. For fancier tutorials check back soon on this blog. In the main.go file add this code.

```go
package main
import (
	"database/sql"
	"encoding/json"
	"fmt"
	"log"
	"net/http"
	"time"
    
    _ "github.com/lib/pq"
)

type Note struct {
	Name        string
	Date        time.Time
	Description string
}

// Function for initializing a connection to the database
func initializeDB(dbPort int, dbName, dbUser, dbPassword, dbHost string) (*sql.DB, error) {
	var err error
	dbInformation := fmt.Sprintf("host=%s port=%d user=%s password=%s dbname=%s sslmode=disable", dbHost, 5432, dbUser, dbPassword, dbName)
	db, err := sql.Open("postgres", dbInformation)
	if err != nil {
		log.Fatal("This is the error: ", err)
		fmt.Printf("Cannot connect to %s database", dbInformation)
		return nil, err
	}
	err = db.Ping()
	if err != nil {
		log.Fatal(err)
		return nil, err
	}
	log.Println("Database Connection established")
	return db, nil
}

// Hello world function that returns current time and a text response from the struct created above
func helloWorld(w http.ResponseWriter, r *http.Request) {
	fmt.Println("hello world")
	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(http.StatusOK)
	note1 := Note{"Trial", time.Now().Local(), "This is a test"}
	data, err := json.Marshal(note1)
	if err != nil {
		fmt.Println(err)
	}
	w.Write(data)
}

func main() {
	// Initialize database connection with default values (Not for production purposes
	database, err := initializeDB(5432, "postgres", "postgres", "postgres", "postgres")
	if err != nil {
		log.Fatalf("Could not set up database %v", err)
	}
    // Ensure connection does not close once it has been established
	defer database.Close()
    
    // Initialize the http server
	http.HandleFunc("/", helloWorld)
	log.Fatal(http.ListenAndServe(":8080", nil))
}
```

## The DockerFile

Once that is done, we can now finally start the main event, the dockerization. * Cue in drums, trumpets and people singing *. We first need to create a dockerfile for the main app (we could do it all in the docker-compose file but I prefer it this way).
```Dockerfile
    FROM golang:latest as builder 
    
    LABEL maintainer = "Some maintainer <someMaintainer@email.com>"
    
    WORKDIR /app
    
    COPY go.mod go.sum ./
    
    RUN go mod download
    
    COPY . .
    
    RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .
    
    # Starting a new stage from scratch 
    
    FROM alpine:latest
    
    RUN apk --no-cache add ca-certificates
    
    WORKDIR /root/
    
    COPY --from=builder /app/main .
    
    EXPOSE 10000 10000
    
    CMD ["./main"]
```
We have just created a multi-stage docker build for Go which significantly reduces the time it takes to run as well as reduces the size of the final docker image created.

## The Docker-compose file
We can now afterwards create the docker-compose file which will hold the database docker container and allow us to run both docker containers simultaneously. In the docker-compose.yml add the following lines of code.
```yaml
    version: "3.7"
    services: 
        server:
            build:
                dockerfile: Dockerfile
                context: .
            env_file: .env
            depends_on:
                - database
            networks:
                - default
            ports:
                - "10000:10000"
            networks:
                - backend
        database:
            image: postgres
            restart: always
            env_file: 
                - .env
            ports:
                - "9002:5432"
            volumes: 
                - data:/var/lib/postgresql/data
            networks:
                - backend
    volumes:
        data:
    networks:
        backend:
```

Now to put all the pieces together.
```bash
 $ docker-compose up
```
