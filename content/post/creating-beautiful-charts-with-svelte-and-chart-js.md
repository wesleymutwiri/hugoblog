+++
date = 2022-01-09T09:00:00Z
description = "A simple svelte tutorial to demonstrate how someone can use Chart js with Svelte"
image = "isaac-smith-6EnTPvPPL6I-unsplash.jpg"
tags = ["svelte"]
title = "Create Beautiful Charts with Svelte and Chart js"

+++
[_Svelte_](https://svelte.dev) is a **JavaScript compiler** that compiles its code to HTML, CSS and vanilla JavaScript hence creating a very performant website. On the official website, there is a [**tutorial**](https://svelte.dev/tutorial/basics) that explains Svelte step-by-step. If you just want to see the completed code and figure it out by yourself, go to the [**code**](https://codesandbox.io/s/svelte-chart-js-tutorial-5gr4o) for this article.

Now that the introduction and pleasantries are out of the way, it is time for the main event. I'll be assuming that the desktop environment being used in this tutorial is Linux based for the initial setup of a svelte application and you already have node and npm installed.

## Prerequisites

So as to be able to fully work on the application at hand, you'll need:

* Familiarity with HTML, CSS and JavaScript(ES6+),
* Node.js and npm installed on your development machine.
* Working machine

For this tutorial, we shall be using the [Chart js](https://www.chartjs.org/ "Chart JS") library for creating basic charts.

## Why Chart.js

That's a pretty good question actually, why would anyone decide to use chart JS whereas there are very many other charting libraries out there that are more performant? The simple answer is that Chart JS(According to sources called "this is my opinion") is easier to use. Some of the alternative charting libraries include:

1. [pancake](https://pancake-charts.surge.sh/) which has very scarce documentation and is in thorough experimentation(at the time of writing). Since it has been created by Rich Harris, you can rest assured that it might probably never get documentation or a stable release just like our fallen soldier [sapper](https://sapper.svelte.dev/) (a moment of silence in remembrance)
2. [Chartist](https://gionkunz.github.io/chartist-js/) -  Really impressive charting library that is only 10KB (Gzip) with no dependencies. Round of applause for this awesome library that should play nice with svelte since it does not have any dependencies. I honestly can't remember why I didn't go with this for this tutorial but there's always time for another tutorial, am I right 😉?
3. [d3.js](https://d3js.org/) - Great library, but it would take me to long to explain whereas I'm trying to write a super simple tutorial. I would also recommend this library, though those examples they present are thoroughly intimidating, like this example where you create a [chart of Obama's face](https://observablehq.com/@mbostock/voronoi-stippling) using Voronoi Stippling(not a clue what that is and my furniture just started floating around once I spoke those words out loud)

## Getting Started

You could use [codesandbox](https://codesandbox.io) for your initial setup or create a local svelte application using the [degit](https://github.com/Rich-Harris/degit) tool. Open a new terminal and run the following command:

```bash
    npx degit sveltejs/template standard-charter
```

Navigate into the newly created project folder and install the dependencies locally using:

```bash
    cd standard-charter
    npm install 
```

Run the development server using:

```bash
    npm run dev 
```

The dev server will be listening from the [http://localhost:5000](http://localhost:5000) address. Any changes made to the application will be automatically rebuilt and reloaded into the running app as long as the dev server is still running.

You'll also need to install [Chart js](https://www.chartjs.org/ "Chart JS") using:
```bash
        npm install chart.js
```
In the App.svelte file, delete everything leaving only the script tags. Since this is a relatively simple app, this is where all our code will reside in.

```html
	<script>
      import { onMount } from 'svelte';
      import Chart from 'chart.js/auto/auto.js';
      
      let portfolio;
      const data = {
			labels: ['Expenses', 'Savings', 'Investments'],
			datasets: [
				{
					label: 'My First Dataset',
					data: [300, 50, 100],
					backgroundColor: ['#7000e1', '#fc8800', '#00b0e8'],
					// hoverOffset: 4,
					borderWidth: 0
				}
			]
		};
        const config = {
			type: 'doughnut',
			data: data,
			options: {
				borderRadius: '30',
				responsive: true,
				cutout: '95%',
				spacing: 2,
				plugins: {
					legend: {
						position: 'bottom',
						display: true,
						labels: {
							usePointStyle: true,
							padding: 20,
							font: {
								size: 14
							}
						}
					},
					title: {
						display: true,
						text: 'My Personal Portfolio'
					}
				}
			}
		};
      onMount(()=> {
        const ctx = portfolio.getContext('2d');
        // Initialize chart using default config set
        var myChart = new Chart(ctx, config);
      })
	</script>
	<canvas bind:this={portfolio} width={400} height={400} />
```

And there you have it folks, a very good looking doughnut chart that showcases your "sample budget".

We add the creation of the chart on the svelte `onMount` function so that the chart is created and added to the layout before it is mounted on the page. Note that the value of `canvas` will be `undefined` until the component has mounted, so we put the logic inside the `onMount` [lifecycle function](https://svelte.dev/tutorial/onmount).

> The read-only `this` binding applies to every element (and component) and allows you to obtain a reference to rendered elements. For example, we can get a reference to a `<canvas>` element:

[_svelte bind example_](https://svelte.dev/tutorial/bind-this "svelte bind example")

Chart JS receives the data from the `data` variable we created. We have customized the labels as well as the background color of the doughnut lines.

The `config` variable is what we've used to define the type of chart and to style the chart we've created. The `type` can be either bar, line, pie, radar etc etc depending on the type of chart you want to render. You can play around with the sample data we've provided to see what kind of chart will be formed.

In a real life situation, the data to be displayed would be provided from an external service such as an API rather than have it hard coded as a variable.

Reducing the `cutout` percentage will make the doughnut chart slightly less hollow and a 0% cutout will make it a pie chart.

If you need to make multiple charts on the same page then it would be simpler and easier to copy the code and convert it into a component where you export defaults. But that is all the time I have for this week folks, stay tuned for more next week.
