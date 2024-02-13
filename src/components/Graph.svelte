<script>
    import * as d3 from 'd3';

    export let data;
    
    const width = 928;
    const height = 600;
    const marginTop = 20;
    const marginRight = 30;
    const marginBottom = 30;
    const marginLeft = 40;

    let svg;

     // Placeholders for the axis elements.
    let gx;
    let gy;

    // $: x = d3
    // .scaleUtc()
    // .domain(d3.extent(data, (d) => d.date))
    // .range([marginLeft, width - marginRight]);

    $: x = d3
        .scaleLinear()
        .domain(['Chinese', 'Indian', 'Japan', 'Korean', 'Thai', 'Vietnamese'])
        .range([marginLeft, width - marginRight]);

    $: y = d3
        .scaleLinear()
        .domain(d3.extent(data, (d) => d.value))
        .nice()
        .range([height - marginBottom, marginTop]);


</script>



<div class="temperature-plot">
    <!-- <svg width="200" height="200" viewBox="-100 -100 200 200">
        <circle cx="0" cy="20" r="70" fill="#D1495B" />
        <circle
          cx="0"
          cy="-75"
          r="12"
          fill="none"
          stroke="#F79257"
          stroke-width="2"
        />
      </svg> -->


    <svg
      {width}
      {height}
      viewBox="0 0 {width} {height}"
      style="max-width: 100%; height: auto;"
    >
      <!-- points -->
      <g stroke="#000" stroke-opacity="0.2">
        {#each data as d, i}
          <circle
            key={i}
            cx={x(d.category)}
            cy={y(d.stars)}
            r="50.5"
          />
        {/each}
      </g>
    </svg>
  </div>


  