<script>
  import { onMount } from 'svelte'; 
  import * as d3 from 'd3';
  import Graph from '../components/Graph.svelte';
</script>


<main>
<h1>Yelp Cuisine Exploration</h1>
<Graph />
</main>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Protest+Strike&display=swap');
  @import url('https://fonts.googleapis.com/css2?family=Roboto+Serif:ital,opsz,wght@0,8..144,100..900;1,8..144,100..900&display=swap');

  :root {
    --color-bg: #ffffff;
    --color-outline: #c2c2c2;
    --color-shadow: hsl(0, 0%, 0%, 0.1);
    --color-text: #3f4252;
    --color-bg-1: hsla(0, 0%, 0%, 0.2);
    --color-shadow-1: hsl(0, 0%, 96%);
    background-color: #FCFCFC;
    
  }

  main {
    text-align: center;
    font-family: "Roboto Serif", serif;
    font-style: normal;
    font-weight: 600;
    font-size: 15px;
    color: var(--color-text);
  }

  h1 {
    margin-top: -25px;
    font-size: 5em;
    font-weight: 600;
    line-height: 2;
  }

</style>