<script>
  import * as d3 from 'd3';
  import { feature } from 'topojson-client';

  d3.json('/us-counties.json').then(us => {
  const counties = topojson.feature(us, us.objects.counties).features;

  const svg = d3.select('body').append('svg')
    .attr('width', 960)
    .attr('height', 600);

  const path = d3.geoPath();

  svg.selectAll('.county')
    .data(counties)
    .enter().append('path')
    .attr('class', 'county')
    .attr('d', path)
    .attr('fill', '#ccc')
    .attr('stroke', '#fff');
});
</script>
<div id="map"></div>


  