+++
date = 2022-01-09T09:00:00Z
description = "A simple svelte tutorial to demonstrate how someone can use Chart js with Svelte"
image = "/static/uploads/notes.jpeg"
tags = ["svelte"]
title = "Creating Beautiful Charts with Svelte and Chart js"

+++
[_Svelte_](https://svelte.dev) is a **JavaScript compiler** that compiles its code to HTML, CSS and vanilla JavaScript hence creating a very performant website. On the official website, there is a [**tutorial**](https://svelte.dev/tutorial/basics) that explains Svelte’s step-by-step. If you just want to see the completed code and figure it out by yourself, go to the [**code**]() for this article.

Now that the introduction and pleasantries are out of the way, it is time for the main event. I'll be assuming that the desktop environment being used in this tutorial is Linux based for the initial setup of a svelte application and you already have node and npm installed.

## Prerequisites

So as to be able to fully work on the application at hand, you'll need:

* Familiarity with HTML, CSS and JavaScript(ES6+),
* Node.js and npm installed on your development machine.
* Working machine

For this tutorial, we shall be using the [Chart js](https://www.chartjs.org/ "Chart JS") library for creating basic charts.

## Why Chart.js

That's a pretty good question actually, why would anyone decide to use chart JS whereas there are very many other charting libraries out there that are more performant? The simple answer is that Chart JS(According to sources called "this is my opinion") is easier to use. Some of the alternative charting libraries include:

1. [pancake]() which has very scarce documentation and is in thorough experimentation(at the time of writing). Since it has been created by Rich Harris, you can rest assured that it might probably never get documentation or a stable release just like our fallen soldier [sapper]() (a moment of silence in remembrance)
2. [Chartist]() -  Really impressive charting library that is only 10KB (Gzip) with no dependencies. Round of applause for this awesome library that should play nice with svelte since it does not have any dependencies. I honestly can't remember why I didn't go with this for this tutorial but there's always time for another tutorial, am I right 😉?
3. [d3.js]() - Great library, but it would take me to long to explain whereas I'm trying to write a super simple tutorial. I would also recommend this library, though those examples they present are thoroughly intimidating, like this example where you create a [chart of Obama's face]() using Voronoi Stippling(not a clue what that is and my furniture just started floating around once I spoke those words out loud)

## Getting Started

You could use [codesandbox](https://codesandbox.io) for your initial setup or create a local svelte application using the [degit]() tool. Open a new terminal and run the following command:

    npx degit sveltejs/template standard-charter

Navigate into the newly created project folder and install the dependencies locally using:

    cd standard-charter
    npm install 

Run the development server using:

    npm run dev 

The dev server will be listening from the [http://localhost:5000](http://localhost:5000) address. Any changes made to the application will be automatically rebuilt and reloaded into the running app as long as the dev server is still running.

You'll also need to install [Chart js](https://www.chartjs.org/ "Chart JS") using:

    npm install chart.js

In the App.svelte file, delete everything leaving only the script tags. Since this is a relatively simple app, this is where all our code will reside in.

```html
	<script>
      import { onMount } from 'svelte;
      import Chart from 'chart.js/auto/auto.js';
      onMount(()=> {
      
      })
	</script>
```