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
        .on('mouseover', function(event, d) {
            d3.select(this).transition().duration(300).style('fill', 'orange');
            tooltip.transition().duration(200).style('opacity', .9);
            tooltip.html(d.properties.name)
                .style('left', (event.pageX) + 'px')
                .style('top', (event.pageY - 28) + 'px');
            console.log(d.properties.name);
        })
            .on('mouseout', function() {
                tooltip.transition().duration(500).style('opacity', 0);
                d3.select(this).transition().duration(300).style('fill', '#d3d3d3');
        });
  
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
  
  <style>
    .state {
      stroke: #000;
      stroke-linejoin: round;
      stroke-width: 10px;
    }
    .state-label {
      font-size: 4px;
      pointer-events: none;
    }

    .tooltip {
        position: absolute;
        text-align: center;
        width: 120px;
        height: auto;
        padding: 2px;
        font: 12px sans-serif;
        background: lightsteelblue;
        border: 0px;
        border-radius: 8px;
        pointer-events: none;
        opacity: 0;
    }
  </style>
  