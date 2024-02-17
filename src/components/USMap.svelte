<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
    import { feature } from 'topojson-client';
  
    const stateAbbreviations = {
  'Alabama': 'AL',
  'Alaska': 'AK',
  'Arizona': 'AZ',
  'Arkansas': 'AR',
  'California': 'CA',
  'Colorado': 'CO',
  'Connecticut': 'CT',
  'Delaware': 'DE',
  'Florida': 'FL',
  'Georgia': 'GA',
  'Hawaii': 'HI',
  'Idaho': 'ID',
  'Illinois': 'IL',
  'Indiana': 'IN',
  'Iowa': 'IA',
  'Kansas': 'KS',
  'Kentucky': 'KY',
  'Louisiana': 'LA',
  'Maine': 'ME',
  'Maryland': 'MD',
  'Massachusetts': 'MA',
  'Michigan': 'MI',
  'Minnesota': 'MN',
  'Mississippi': 'MS',
  'Missouri': 'MO',
  'Montana': 'MT',
  'Nebraska': 'NE',
  'Nevada': 'NV',
  'New Hampshire': 'NH',
  'New Jersey': 'NJ',
  'New Mexico': 'NM',
  'New York': 'NY',
  'North Carolina': 'NC',
  'North Dakota': 'ND',
  'Ohio': 'OH',
  'Oklahoma': 'OK',
  'Oregon': 'OR',
  'Pennsylvania': 'PA',
  'Rhode Island': 'RI',
  'South Carolina': 'SC',
  'South Dakota': 'SD',
  'Tennessee': 'TN',
  'Texas': 'TX',
  'Utah': 'UT',
  'Vermont': 'VT',
  'Virginia': 'VA',
  'Washington': 'WA',
  'West Virginia': 'WV',
  'Wisconsin': 'WI',
  'Wyoming': 'WY',
};

const stateData = {
  'California': {'Greek':10, 'Korean':20, 'Chinese':30, 'American':40, 'Italian':50},
  'New York': {'Greek':15, 'Korean':25, 'Chinese':35, 'American':45, 'Italian':55}
};

let previouslyClickedState = null;

function drawGraphForState(stateName) {
    const data = stateData[stateName];
    const cuisineTypes = Object.keys(data);
    const values = Object.values(data);

    d3.select('#graph').selectAll('*').remove();

    const margin = {top: 20, right: 30, bottom: 40, left: 90},
          width = 460 - margin.left - margin.right,
          height = 400 - margin.top - margin.bottom;

    const svg = d3.select("#graph")
      .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
      .append("g")
        .attr("transform",
              "translate(" + margin.left + "," + margin.top + ")");

    const x = d3.scaleBand()
      .range([ 0, width ])
      .domain(cuisineTypes)
      .padding(0.2);
    var xAxis = svg.append("g")
      .attr("transform", "translate(0," + height + ")")
      .transition().duration(1000)
      .call(d3.axisBottom(x))
      .selectAll("text")
        .attr("transform", "translate(-10,0)rotate(-45)")
        .style("text-anchor", "end");

    const y = d3.scaleLinear()
      .domain([0, d3.max(values)])
      .range([ height, 0]);
    var yAxis = svg.append("g")
      .transition().duration(1000)
      .call(d3.axisLeft(y));

    svg.selectAll("mybar")
      .data(values)
      .enter()
      .append("rect")
        .attr("x", (d, i) => x(cuisineTypes[i]))
        .attr("y", d => y(d))
        .attr("width", x.bandwidth())
        .attr("height", d => height - y(d))
        .attr("fill", "#69b3a2");
  }

    onMount(async () => {
      const mapData = await d3.json('/us-states.json');
      const states = feature(mapData, mapData.objects.states).features;
  
      const svg = d3.select('#map').append('svg')
        .attr('viewBox', '0 0 960 600')
        .style('width', '75%')
        .style('height', 'auto');
  
      const projection = d3.geoAlbersUsa()
        .scale(1300)
        .translate([480, 300]);
  
      const path = d3.geoPath()
        .projection(projection);
  
      svg.selectAll('.state')
        .data(states)
        .enter().append('path')
        .attr('class', 'state')
        .attr('d', path)
        .attr('fill', '#d3d3d3')
        .style('stroke-width', "20px")
        .on('click', function(event, d) {
          if (previouslyClickedState) {
            svg.select(`#${previouslyClickedState}`).attr('fill', '#d3d3d3');
          }
          previouslyClickedState = `state-${d.properties.name.replace(/\s/g, '-')}`;
          d3.select(this).attr('fill', 'orange').attr('id', previouslyClickedState);

          drawGraphForState(d.properties.name);
          tooltip.transition().duration(200).style('opacity', .9);
          tooltip.html(d.properties.name)
              .style('left', (event.pageX) + 'px')
              .style('top', (event.pageY - 28) + 'px');
          console.log(d.properties.name);
        })
  
        const tooltip = d3.select('body').append('div')
            .attr('class', 'tooltip')
            .style('position', 'absolute')
            .style('background', '#fff')
            .style('border', '1px solid #000')
            .style('padding', '5px')
            .style('pointer-events', 'none')
            .style('opacity', 0);

      svg.selectAll('.state-label')
        .data(states)
        .enter().append('text')
        .attr('class', 'state-label')
        .attr('transform', d => `translate(${path.centroid(d)})`)
        .attr('dy', '.15em')
        .attr('text-anchor', 'middle')
        .text(d => stateAbbreviations[d.properties.name] || '')
        .style('fill', 'black');

    });
  </script>
  
  <div id="map"></div>
  