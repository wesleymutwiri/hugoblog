+++
date = 2021-02-22T21:00:00Z
description = "A basic Svelte based Knowledge base app. Frontend in Svelte/Sapper and backend made in Golang. This is a simple tutorial on creating a fullstack application in svelte."
image = ""
tags = ["Golang", "Svelte"]
title = "Fullstack Svelte and Golang Note app"

+++
# Fullstack Svelte and Golang Note Application

Before we get started, if you don't know already, Svelte is the new kid on the block in terms of front-end frameworks. Its main selling points are smaller bundle size and faster applications by compiling all svelte code into vanilla JavaScript instead of using a virtual DOM in the case of other front-end frameworks such as Vue, Angular and React.

## Requirements

In order to follow this tutorial you'll need basic knowledge in:

* HTML, CSS and JS(at least enough knowledge to read the code as I'll explain the reason for using most things on the application)
* Computer and/or mobile phone if you decide to use a combination of termux/ any other terminal for mobile phones. In short somewhere to write code.
* Time

So I guess it's time we do this then.

## Project Setup

The project setup won't be as complex. We'll have two applications side by side. A front-end application (using Sapper) and a back-end application (using Golang). Since the application being built is slightly large. I might be forced to break the tutorial into a couple of parts but that will be decided on later on after at least I have a working version ready.

But stick around and I'll take you through a long mystical coding journey filled with a lot of thinking, live debugging and very very mid level developer thinking, so stay tuned.

Create and move into a parent directory where most of the code will exist. The name is up to you.

```bash
$ mkdir knowtify
$ cd knowtify
```

Once inside the parent directory we'll create two folders where one our server-side(back-end) logic will exist and the other will contain our client-side(front-end) logic.

Let's first create the front-end application. For that we'll require that you have [node JS](https://nodejs.org) installed. We'll use sapper even though it won't have a version1.0 as we wait to upgrade to svelte-kit once it is released. So there will be another part final for this upgrading process. Run the following command to create a new sapper application in your parent folder, install dependencies and run the application server to confirm it actually works

```bash
$ npx degit "sveltejs/sapper-template#rollup" frontend
$ cd frontend
$ npm i
$ npm run dev
```

Once the commands have finished executing. Open your browser at [localhost:3000](http://localhost:3000 "localhost:3000") and you'll see a very basic web application confirming you've successfully created the frontend application. You can now kill the server using Ctrl + C and proceed to creating the backend application.

For the backend application on the other hand is where some work will come in. If you're here to focus only on the frontend application you can skip this part and move on swiftly.

First create a new directory named backend and then initialize a new go application. We'll be using go modules so any go version above 1.11 is needed. If you haven't already, install [golang](https://golang.org/dl/), the most recent at the time of writing is 1.16 hence that is what I'm currerntly using for the backend application.

```bash
$ cd ..
$ mkdir backend
$ cd backend
```

The commands assume that you are already in the frontend directory. If not, the structure for the application is very simple with only two folders, backend and frontend directories within the knowtify(parent directory) folder.

Anyway, moving on swiftly. Inside the backend folder, create a basic main.go file which will serve as the entry point into the application as a whole.

```bash
$ touch main.go
```

```go
// serverside/main.go
package main

import (
	"fmt"
	"time"
)

func main() {
	fmt.Println("This is exciting and we're just starting")
    fmt.Printf("The time is", time.Now())
}
```