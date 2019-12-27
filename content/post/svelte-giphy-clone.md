---
title: Svelte Quickstart Tutorial
date: 2019-12-24
description: Guide to emoji usage in Hugo
image: "artist.jpg"
draft: false
tags: ["svelte"]
---

[Svelte js](https://svelte.dev/) is a new JavaScript UI library that’s similar in many ways to modern UI libraries like React and Vue except for the fact that it doesn’t use the concept of a virtual DOM. It compiles all the code written into plain vanilla JS during runtime.

In this tutorial, we shall be creating a simple GIF displaying website using the [Giphy API](https://developers.giphy.com/docs/)

## About Svelte
Simple Svelte description, as written in their official [site](https://svelte.dev):

> Svelte is a radical new approach to building user interfaces. Whereas traditional frameworks like React and Vue do the bulk of their work in the browser,

Svelte is faster than most of the modern day frameworks such as Angular, React and Vue. It also compiles into vanilla javascript which creates a smaller bundle of code needed to be served as compared to the other frameworks. This has brought the name the "disappearing framework".

How Svelte works is described perfectly well in the official [svelte blog](https://svelte.dev/blog/svelte-3-rethinking-reactivity) as:

> Svelte runs at build time, converting your components into highly efficient imperative code that surgically updates the DOM. As a result, you’re able to write ambitious applications with excellent performance characteristics

In this tutorial we'll be learning how to setup a simple single page Application using svelte, how to fetch data and how to iterate over data fetched using svelte syntax. 

## Why Svelte

1. Simple and easy for creating new small applications.
2. Simple and easy to understand syntax. 
3. It's shiny and new
4. Because I felt like it. ;)

## Prerequisites 

So as to be able to fully work on the application at hand, you'll need:

+ Familiarity with HTML, CSS and JavaScript(ES6+),
+ Node.js and npm installed on your development machine.
+ Registered account on [giphy](https://developers.giphy.com)

For this tutorial, we shall be using the [API](https://developers.giphy.com/docs) for all our GIF callbacks. You need to get an API key after creating the account which is free.

## Getting Started 
We shall initialize our new svelte application locally using the [degit]() tool. You can either install degit globally on your system or use the npx tool to execute it from npm. Open a new terminal and run the following command:

```bash 
npx degit sveltejs/template giphy
```

Navigate into the newly created project folder and install the dependencies locally using: 

```bash 
cd giphy
npm install 
```

Run the development server using:
```bash 
npm run dev 
```

The dev server will be listening from the [http://localhost:5000](http://localhost:5000) address. Any changes made to the application will be automatically rebuilt and reloaded into the running app as long as the dev server is still running.

```javascript
import App from './App.svelte';

const app = new App({
	target: document.body,
	props: {
		name: 'world'
	}
});

export default app;
```

In the App.svelte file, delete everything leaving only the script tags. Since this is a relatively simple app, this is where all our code will reside in.

We would like users who visit our site to see the trending GIFs immediately not a blank page. We shall be using the trending URL endpoint from the giphy API. The sample response returned from the URL is as shown below:

```json
{
   "data":
   [
       {
           "type": "gif",
           "id": "jquDWJfPUMCiI",
           "url": "https://giphy.com/gifs/scared-shocked-jquDWJfPUMCiI",
           "slug": "scared-shocked-jquDWJfPUMCiI",
           "bitly_gif_url": "https://gph.is/1eG7jnH",
           "bitly_url": "https://gph.is/1eG7jnH",
           "title": "shocked jim carrey GIF",
           "is_sticker": 0,
           "import_datetime": "2013-09-11 23:18:24",
           "trending_datetime": "2019-12-26 21:46:43",
           "images":
           {
               "downsized_large":
               {
                   "height": "310",
                   "size": "2056034",
                   "url": "https://media1.giphy.com/media/jquDWJfPUMCiI/giphy.gif?cid=f45f6abfed994ba7d8d2e51d17ecec3f6f1028235bf92555&rid=giphy.gif",
                   "width": "348"
               },
		   }
```

The response is a lot larger and can be found on your [giphy dashboard](https://developers.giphy.com/explorer) under the API explorer. This defines how the data we need will be consumed.

## Svelte Code Time!
The time we have all been waiting for, writing some Svelte code. Giphy has additional options and I will be using the “limit gifs” and “rating” options. In the App.svelte file inside the script tags, add the following code below: 

```javascript
import { onMount } from "svelte";

const API_KEY = "<YOUR_API_KEY>";
const TRENDING_URL = `https://api.giphy.com/v1/gifs/trending?api_key=${API_KEY}&limit=25&rating=G`;

let posts = [];

``` 

Replace the <YOUR_API_KEY> with the API_KEY you get after creating an app on Giphy. This will be used to authenticate your requests.The URL is used to get the top 25 trending GIFs that are rated for general viewing. We wouldn't want some explicit content on our site now would we?

The posts array will be used to store the incoming JSON request before being presented to the user.

```javascript 
import { onMount } from svelte
```

This function from the svelte package is used to define functions that will be run immediately when the application starts to run. This is useful since we need viewers to see the GIFs immediately the page loads.

Below the previous code inside the script tags, create a function onMount() in order to get the content from the giphy API. Code is as shown below. 

```javascript 
onMount(async function() {
		let calling = await fetch(TRENDING_URL);
		let p = await calling.json();
		posts = p["data"];
	});
```

The function simply creates an asynchronous callback of the URL in order to fetch the JSON response from the Trending URL specified as a const variable in the previous code. The data is then stored in the posts array we declared previously. Now we have the data required from the JSON, time to display it ion the page.

Still in the App.svelte file, outside of the script tags, we'll write the code to display the different 25 GIFs we've received from the API.

```html
<main>	
	<div class="container">
	{#each posts as post}
	<div class="card">
		<img src="{post.images.original.url}" alt="{post.title}">
		<div class="card-body">
			<h3>{post.title}</h3>
		</div>
	</div>
	{/each}
	</div>
</main>
```

The code: 

```
{#each posts as post}
{/each}
```
Is a simple iterator that takes every element in the posts array that we created and acts as a for loop that allows us to display the post one by one. The {/each} defines where the iterator ends. Once you run the server you will then see a full list of 25 GIFs displayed on screen. The application is however not lookable, probably because all the images have different sizes and are displayed randomly around the screen.

Let's add a bit of CSS in style tags below the HTML code to fix that:

```css
.container {
	display: flex;
	flex-wrap: wrap;
}

.container > .card img {
	max-width: 100%;
}
```

Doesn't that look pretty? Okay I'm kidding. But at least now we have a working application. In the subsequent blog posts we shall style the application better, add search functionality, download GIF functionality and implement a copy to clipboard function for copying the URL to the GIFs as well as many more features. Stay tuned.  

Feel free to leave comments below and give me a beep on any of my social media handles.