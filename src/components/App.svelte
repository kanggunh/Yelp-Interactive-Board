<script>
    import { onMount } from 'svelte'; 
    import * as d3 from 'd3';
    import Graph from '../components/Graph.svelte';
    import USMap from './USMap.svelte';
</script>


<main>
  <h1>Yelp Cuisine Exploration</h1>
  <Graph />
  <!-- <USMap /> -->
</main>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Protest+Strike&display=swap');
  
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
      font-family: "Protest Strike", sans-serif;
      font-style: normal;
      font-weight: 400;
      line-height: 2;
      font-size: 15px;
      color: var(--color-text);
    }
  
    h1 {
      font-size: 5em;
      font-weight: 200;
      line-height: 2;
    }

</style>