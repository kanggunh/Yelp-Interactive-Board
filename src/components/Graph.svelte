<script>
  import { onMount } from 'svelte'; 
  import * as d3 from 'd3';
  import Tooltip from "./Tooltip.svelte";

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


  let width= 980;
  let height= 495;
  const margin = { top: 20, right: 20, bottom: 20, left: 125 };
  const innerHeight = height - margin.top - margin.bottom;
  const innerWidth = width - margin.left - margin.right;

  let stateData = [];
  let selected_state = 'CA';
  let hovered=null;
  
  onMount(async () => {
    const res = await fetch(
        'by_category_2.csv',
    );
    const csv = await res.text();
    await d3.csvParse(csv, data => {
      stateData.push(data);
    });
    stateData = stateData;
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

<div class='chart-container' bind:clientWidth={width} on:mouseout={()=>{hovered=null}}>
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
        <rect class='bars'
          role="presentation"
          x="0"
          y={hovered? hovered == d ? yScale(d.category)-7:yScale(d.category):yScale(d.category)}
          width={xScale(d[selected_state])}
          height={hovered ? hovered == d ?yScale.bandwidth()+14:yScale.bandwidth():yScale.bandwidth()}
          opacity={hovered ? hovered == d ? "1" : ".3" : "1"}
          fill="#F9746F"
          on:mouseover={() => {hovered=d}}
        >{console.log(d)}</rect>
      {/each}
    </g>
  </svg>

  <p class='x-axis'>count</p>
  
  {#if hovered}
    <Tooltip data = {hovered} {xScale} {yScale} {selected_state}/>
  {/if}

</div>


<style>
  @import url('https://fonts.googleapis.com/css2?family=Roboto+Serif:ital,opsz,wght@0,8..144,100..900;1,8..144,100..900&display=swap');
  
  .chart-container {
    position: relative;
    width: 1000px;
    height: 520px;
    z-index: 15;
    margin-left: auto;
    margin-top: auto;
    margin-right: auto;
  }

  .x-axis {
    position: relative;
    width: 1000px;
    margin-left: 35px;
    margin-top: auto;
    margin-right: auto;
    font-size: 20px;
  }
  
  button {
    background-color: #FCFCFC;
    font-family: "Roboto Serif", serif;
    color: black;
    border: 2px solid #1B1717; 
    transition-duration: 0.45s;
    border-radius: 15px;
    padding: 8px 15px;
    margin-top: 25px;
    margin-bottom: 5px;
    font-weight: 600;
  }

  h2 {
    margin-top: -65px;
    font-weight: 600;
  }
  
  button:hover {
    background-color: #FFE2E1; 
    color: white;
  }

  rect {
    transition: all 450ms ease;
  }

  text {
    font-weight: 700;
  }
</style>