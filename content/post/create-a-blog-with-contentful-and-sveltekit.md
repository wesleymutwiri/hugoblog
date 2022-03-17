+++
date = 2022-03-16T21:00:00Z
description = "A tutorial on how to setup Contentful on sveltekit to make a blog"
image = ""
tags = ["contentful", "svelte kit", "svelte"]
title = "Create a blog with Contentful and Sveltekit"

+++
I know you're wondering why another "create a blog tutorial" but that's the simplest project I could come up with for this tutorial. I also needed another excuse to spread the svelte gospel to everyone out there. Since there is currently no other Contentful and sveltekit tutorial out there (at time of writing of course), I am here to save the day, with my poor writing skills.

[Contentful](https://www.contentful.com/) is an API first content platform to build digital experiences (according to their site description of course). If you only need to check out the code, without the hustle of reading this entire blog, you can find the [live deployed site here](https://brave-perlman-af48a0.netlify.app/) and the [repo for reference here](https://github.com/wesleymutwiri/content-kit)

## Other Content Management Systems

1. [Strapi](https://strapi.io/) - an open source Nodejs Headless CMS that provides both REST or GraphQL.
2. [Wordpress](https://wordpress.org/) - One of the most popular CMS and website builder.\]
3. [Optimizely](https://www.optimizely.com/) - Not a clue what it does but the company showed up as I did my research.
4. Obviously [Contentful](https://www.contentful.com/)

I went with contentful because it was the first one I could find after a few searches and seemed the simplest to use at the time.

## Prerequisites

1. Contentful account, API key and secret key
2. Text editor or JavaScript coding environment
3. Time
4. Familiarity with HTML, CSS and JavaScript(ES6+),
5. Node.js and npm installed on your development machine.
6. Basic knowledge of svelte and svelte kit

## Initial Setup

You could use [codesandbox](https://codesandbox.io) or [svelte playground](https://svelte.dev/repl/) for a plain svelte application to play around with Contentful (Without routing of course) or create a local svelte kit application, for dynamic routes. Open a new terminal and run the following command:

```bash
    npm init svelte@next content-kit
```

Accept the prompt to proceed and afterwards select the skeleton project since we don't want to have a lot of demo data. Choose no when prompted whether to use TypeScript, enable ESLint, enable Prettier and disable testing since we won't be covering testing.

Navigate into the newly created project folder and install the dependencies locally using:

```bash
    cd content-kit
    npm i
```

You'll also need to install [Contentful SDK](https://www.contentful.com/developers/docs/extensibility/app-framework/sdk/ "Contentful") using:

```bash
    npm install contentful
```

Run the development server using:

```bash
    npm run dev 
```

The dev server will be listening from the [http://localhost:3000](http://localhost:3000) address. Any changes made to the application will be automatically rebuilt and reloaded using [Vite](https://vitejs.dev/) into the running app as long as the dev server is still running.

## Contentful Setup

I would go into the details of how to create an account and setup your contentful API but that would be free marketing for them and we don't do that here. However, I'll try my best to explain how to easily do so with screenshots.

On the content model page of your dashboard, create a new content model that will host the structure of how your blog will look like and name it blog.

Add the fields as shown below, making them mandatory and configuring the dateField to only include the date. Also, make sure the slugField is auto-generated from the title for SEO purposes.

![Contentful Content Model Screenshot]()

On the content page is where you'll add sample blog posts so that we can have data to play around with. So add one post and another one.

![Another one image](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/b0vi3a8an5qik3ah6062.gif)

On the settings page, copy the API key and space ID and paste them somewhere safe, we'll add them to the sveltekit project in a moment.

## The Sveltekit project

Since this isn't a styling tutorial, we'll create a very basic UI or if you'd like, you can use the layout from [this repo](https://github.com/wesleymutwiri/wm-blog-template), (yes, I'm shamelessly advertising my own repos), and tweak it to your liking.

Create a .env file in the root of your project and add your API key and space ID as shown below.

```.env
VITE_CONTENTFUL_DELIVERY_API_TOKEN=<insert_contentful_api_key_here>
VITE_CONTENTFUL_SPACE_ID=<insert_contentful_space_id_here>
```

If you need a quick explanation of what all folders and files represent or if you're new to svelte kit, you can checkout [here](https://kit.svelte.dev/docs/introduction).

We'll create a lib folder inside the src folder that will contain all the components and library files that we'll require.

> This is a simple alias to src/lib, or whatever directory is specified as config.kit.files.lib. It allows you to access common components and utility modules without ../../../../ nonsense.

Create a `contentfulClient.js` inside the `lib` folder to intialize the contentful SDK.

```javascript
import contentful from 'contentful';
export const client = contentful.createClient({
	space: import.meta.env.VITE_CONTENTFUL_SPACE_ID,
	accessToken: import.meta.env.VITE_CONTENTFUL_DELIVERY_API_TOKEN
});
```

Create an `data.json.js` file in the routes folder. This file will include the logic that gets data from the contentful CMS. The retrieved blogs are then passed onto the body of the index.svelte file.

```javascript
import { client } from '$lib/contentfulClient';

export async function get() {
	// get all blogs from Contentful
	const blogs = await client.getEntries({
		content_type: 'blog'
	});

	if (blogs) {
		return {
			status: 200,
			body: {
				blogs
			}
		};
	}

	return {
		status: 404
	};
}
```

The `content_type` can be found from the content model sidebar on the right labeled as CONTENT TYPE ID. This will be used to return all entries in the content type model you created.

The routes folder contains the routes, hence the index.svelte site represents the root path.

Delete everything in the index.svelte file and add the following.

```html
<script context="module">
	export async function load({ fetch }) {
		const [{ blogs }] = await Promise.all([fetch('/data.json').then((r) => r.json())]);

		return {
			props: {
				blogs
			}
		};
	}

</script>

<script>
	import { base } from '$app/paths';
	export let blogs;
</script>

<div class="row">

	{#each blogs.items as blog}

        <div class="column">

        <a href={`${base}/${blog.fields.slugField}`}>

            {blog.fields.title}

        </a>

        <p>{blog.fields.datePublished}</p>

        </div>

    {/each}

</div>

<style>

</style>
```

In the module script, we fetch the blogs variable we had instantiated from the `data.json.js` file. The `<script context="module">` is necessary because load runs before the component is rendered. Code that is per-component instance should go into a second `<script>` tag.

> The `{base}` is a string that matches config.kit.paths.base. It must begin, but not end, with a /.

The slugField will be used to uniquely identify a specific blog post while maintaining SEO for the entire site. We now need a way to access a specific blog post and have the content retrieved from the API as markdown to be rendered as HTML.

For the rendering, we'll need to install an extra package. We'll need

* `marked` - a low-level markdown compiler for parsing markdown without caching or blocking for long periods of time.**

```bash
npm i marked
```

Create a folder called `components` inside the `lib` folder which will contain different components. Afterwards, create a file `RichContent.svelte` that will contain the code necessary to convert rich text to HTML.

```html
<script>
	import { marked } from 'marked';
	export let richContent = '';

	const renderer = new marked.Renderer();

	$: pageContent = marked(richContent, { renderer });
</script>

<div>{@html pageContent}</div>
```

We'll need a way for svelte kit to fetch dynamic content based on the route. We'll create a `[slug].json.js` file inside the routes folder that will allow us to fetch dynamic content based on the route visited.

> Dynamic parameters are encoded using \[brackets\]. For example, a blog post might be defined by src/routes/blog/\[slug\].svelte. These parameters can be accessed in a load function or via the page store.

> A file or directory can have multiple dynamic parts, like \[id\]-\[category\].svelte. (Parameters are 'non-greedy'; in an ambiguous case like x-y-z, id would be x and category would be y-z.)

```js
import { client } from '$lib/contentfulClient';

export async function get({ params }) {
	// the `slug` parameter is available because this file
	// is called [slug].json.js
	const blog = await client.getEntries({
		'fields.slugField': params.slug,
		content_type: 'blog'
	});

	if (blog) {
		return {
			body: {
				blog
			}
		};
	}

	return {
		status: 404
	};
}
```

The code is almost similar to the one in the `data.json.js` file but only one post is retrieved since we're matching posts with the slugField that is similar to the url visited. We'll create a `[slug].svelte` file that will be used to define how the page will be rendered and to be able to use the content retrieved from the Contentful CMS.

```html
<script context="module">
	export async function load({ fetch, params }) {
		const url = `/${params.slug}.json`;
		const res = await fetch(url);

		if (res.ok) {

			const { blog } = await res.json();

			return {

				props: {

					blog: blog.items[0]

				}

			};

		}

		return {

			status: res.status,

			error: new Error(`Could not load ${url}`)

		};

	}

</script>

<script>

	import RichContent from '$lib/components/RichContent.svelte';

	export let blog;

</script>

<div class="container application-form">

	<div class="row">

		<div class="col-lg-12">

			<h1>Course: {blog.fields.title}</h1>

			<p>Date From: {blog.fields.datePublished}</p>

			<RichContent richContent={blog.fields.content} />

		</div>

	</div>

</div>

<style>

</style>
```

We import our `RichContent.svelte` component and use it to convert rich text to html. The site is now ready for testing and deployment.

Deployment will be done on [netlify](https://www.netlify.com/) since it is one of the easiest and simplest to use. You can deploy the site on Github, Gitlab and/or Bitbucket which will allow for automatic deployments anytime you push to your hosted repository. You'll need an account, a `netlify.toml` file in your root folder and the svelte kit netlify adapter in order to deploy the site. Extra instructions can be found on the [netlify-adapter GitHub README](https://github.com/sveltejs/kit/tree/master/packages/adapter-netlify)

Let's install the svelte kit netlify adapter first and tweak the svelte config file.

```bash
npm i -D @sveltejs/adapter-netlify@next
```

Change the `svelte.config.js` file to use the netlify adapter instead of the default adapter-auto. The `svelte.config.js` should look something like this:

```js
import adapter from '@sveltejs/adapter-netlify';

import { join } from 'path';

import { readFileSync } from 'fs';

import { cwd } from 'process';

const pkg = JSON.parse(readFileSync(join(cwd(), 'package.json')));

/** @type {import('@sveltejs/kit').Config} */

const config = {

	kit: {

		adapter: adapter({

			split: false

		}),

		vite: {

			ssr: {

				noExternal: Object.keys(pkg.dependencies || {})

			}

		}

	}

};

export default config;
```

Afterwards, create a `netlify.toml` file in the root folder of your application.

> This will determine where to write static assets based on the build.publish settings.

```toml
[build]
  command = "npm run build"

  publish = "build/"

  functions = "functions/"
```

Remember to add your environment variables in the environment sub section inside the build and deploy section on Netlify in order to secure your API token while ensuring your app has access to the environment variables.

And voilà, we are ready to connect to netlify and deploy this application. All you need is to follow the instructions on the netlify site and you'll have your site up and running after the build is complete.

Thank you very much for your time, any comments, reviews, news and critics will be highly appreciated. Till next time.