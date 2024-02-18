
<script>
  import { onMount } from 'svelte'; 
  import * as d3 from 'd3';

  const abbreviationToName = {
  'AL': 'Alabama',
  'AK': 'Alaska',
  'AZ': 'Arizona',
  'AR': 'Arkansas',
  'CA': 'California',
  'CO': 'Colorado',
  'CT': 'Connecticut',
  'DE': 'Delaware',
  'FL': 'Florida',
  'GA': 'Georgia',
  'HI': 'Hawaii',
  'ID': 'Idaho',
  'IL': 'Illinois',
  'IN': 'Indiana',
  'IA': 'Iowa',
  'KS': 'Kansas',
  'KY': 'Kentucky',
  'LA': 'Louisiana',
  'ME': 'Maine',
  'MD': 'Maryland',
  'MA': 'Massachusetts',
  'MI': 'Michigan',
  'MN': 'Minnesota',
  'MS': 'Mississippi',
  'MO': 'Missouri',
  'MT': 'Montana',
  'NE': 'Nebraska',
  'NV': 'Nevada',
  'NH': 'New Hampshire',
  'NJ': 'New Jersey',
  'NM': 'New Mexico',
  'NY': 'New York',
  'NC': 'North Carolina',
  'ND': 'North Dakota',
  'OH': 'Ohio',
  'OK': 'Oklahoma',
  'OR': 'Oregon',
  'PA': 'Pennsylvania',
  'RI': 'Rhode Island',
  'SC': 'South Carolina',
  'SD': 'South Dakota',
  'TN': 'Tennessee',
  'TX': 'Texas',
  'UT': 'Utah',
  'VT': 'Vermont',
  'VA': 'Virginia',
  'WA': 'Washington',
  'WV': 'West Virginia',
  'WI': 'Wisconsin',
  'WY': 'Wyoming'
};


  let width= 1000;
  let height= 520;
  const margin = { top: 20, right: 20, bottom: 20, left: 180 };
  const innerHeight = height - margin.top - margin.bottom;
  const innerWidth = width - margin.left - margin.right;

  let stateData = [];
  let selected_state = 'CA';
  let tooltip;
  
  function handleMouseOver(event, d) {
    tooltip
      .transition().duration(200)
      .style('opacity', 1)
      .text(`${d.category}: ${d[selected_state]}`);
  }

  function handleMouseMove(event, d) {
    tooltip
      .attr('x', event.clientX - margin.left + 200)
      .attr('y', event.clientY - margin.top -150)
      .raise();
  }

  function handleMouseOut() {
    tooltip.transition().duration(100).style('opacity', 0);
  }
  
  onMount(async () => {
    const res = await fetch(
        'by_category_2.csv',
    );
    const csv = await res.text();
    await d3.csvParse(csv, data => {
      stateData.push(data);
    });
    stateData = stateData;
    tooltip = d3.select('#tooltip');
  });

  function update(update_state) {
    selected_state = update_state;
  };

  const select_AZ = () => update('AZ');
  const select_CA = () => update('CA');
  const select_DE = () => update('DE');
  const select_FL = () => update('FL');
  const select_ID = () => update('ID');
  const select_IL = () => update('IL');
  const select_IN = () => update('IN');
  const select_LA = () => update('LA');
  const select_MO = () => update('MO');
  const select_MT = () => update('MT');
  const select_NC = () => update('NC');
  const select_NJ = () => update('NJ');
  const select_NV = () => update('NV');
  const select_PA = () => update('PA');
  const select_TN = () => update('TN');
  
  console.log('hello, ');
  console.log('csv file here:', stateData);

  // $: xDomain = stateData.map((d) => d.category);
  // $: yDomain = stateData.map((d) => +d[selected_state]);

  // $: xScale = d3.scaleBand()
  //   .domain(xDomain)
  //   .range([margin.left, width - margin.right])

  // $: yScale = d3.scaleLinear()
  //   .domain([0, Math.max.apply(null, yDomain)])
	// 	.range([margin.top, height - margin.bottom]);

  $: xDomain = stateData.map((d) => d.category);
  $: yDomain = stateData.map((d) => +d[selected_state]);

  $: yScale = d3.scaleBand().domain(xDomain).range([0, innerHeight]).padding(0.1);
  $: xScale = d3.scaleLinear()
    .domain([0, Math.max.apply(null, yDomain)])
    .range([0, innerWidth]);

</script>

<h2> Restaurant distrubution in {abbreviationToName[selected_state]}</h2>

<div class="btn-group">
  <button on:click={select_AZ}>AZ</button>
  <button on:click={select_CA}>CA</button>
  <button on:click={select_DE}>DE</button>
  <button on:click={select_FL}>FL</button>
  <button on:click={select_ID}>ID</button>
  <button on:click={select_IL}>IL</button>
  <button on:click={select_IN}>IN</button>
  <button on:click={select_LA}>LA</button>
  <button on:click={select_MO}>MO</button>
  <button on:click={select_MT}>MT</button>
  <button on:click={select_NC}>NC</button>
  <button on:click={select_NJ}>NJ</button>
  <button on:click={select_NV}>NV</button>
  <button on:click={select_PA}>PA</button>
  <button on:click={select_TN}>TN</button>
</div>

<svg {width} {height}>
  <g transform={`translate(${margin.left},${margin.top})`}>
    {#each xScale.ticks() as tickValue}
      <g transform={`translate(${xScale(tickValue)},0)`}>
        <line y2={innerHeight} stroke="#E8EDEA" />
        <text text-anchor="middle" dy=".7em" y={innerHeight + 3}>
          {tickValue}
        </text>
      </g>
    {/each}
    
    {#each stateData as d}
      <text
        text-anchor="end"
        x="-3"
        dy=".3em"
        y={yScale(d.category) + yScale.bandwidth() / 2}
      >
        {d.category}
      </text>
      <rect
        role="presentation"
        x="0"
        y={yScale(d.category)}
        width={xScale(d[selected_state])}
        height={yScale.bandwidth()}
        fill="#F9746F"
        on:mouseover="{(event) => handleMouseOver(event, d)}"
        on:mousemove="{(event) => handleMouseMove(event, d)}"
        on:mouseout="{handleMouseOut}"
        on:focus="{(event) => handleMouseOver(event, d)}"
        on:blur="{handleMouseOut}"
      />
    {/each}
    
  </g>
  <text id="tooltip" x={0} y={0} style="opacity: 1; pointer-events: fill; transition: opacity 0.3s; font-size: 12px;" transform={`translate(${margin.left}px, ${margin.top}px)`}></text>
</svg>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Protest+Strike&display=swap');
  
  
  button {
    background-color: #FCFCFC;
    font-family: "Protest Strike", sans-serif;
    color: black;
    border: 2px solid #1B1717; 
    transition-duration: 0.45s;
    border-radius: 15px;
    padding: 10px 24px;
    margin-bottom: 10px;
  }
  
  button:hover {
    background-color: #FFE2E1; 
    color: white;
    }

  rect {
    transition: all 700ms ease;

  }
</style>