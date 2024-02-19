<script>
    export let data;
    export let xScale;
    export let yScale;
    export let selected_state;
    $: console.log(xScale(data[selected_state]))
</script>

<div class='tooltip' style="position: absolute;
  top: {yScale(data.category)}px;
  left: {xScale(data[selected_state])}px">
<h1>{data.category}</h1> 
<p>{data[selected_state]} number of Restaurant</p>
</div>

<style>
    .tooltip {
      padding: 3px;
      background: #d3d3d3;
      border: 1px solid rgba(0, 0, 0, 0.5);
      pointer-events: none;
      transition: top 300ms ease, left 300ms ease;
    }
  
    h1 {
      font-size: 15px;
      font-weight: 100;
      margin-bottom: 0.1rem;
    }

    p {
        font-size: 10px;
        font-weight: 100;
        margin-bottom: 0.3rem;
    }
  </style>