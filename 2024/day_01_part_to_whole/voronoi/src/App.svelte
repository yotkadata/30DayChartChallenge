<script>
  // Code adapted from
  // https://codesandbox.io/s/donation-prototypes-voronoi-treemap-znnql?file=/App.svelte:50-95

  import Chart from "./Chart.svelte";

  const dataPath = "./src/data/sipri_voronoi.json";
  async function loadData(path) {
    const raw = await fetch(path);
    const json = await raw.json();
    return json;
  }
  let data = loadData(dataPath);

  // console.log("Data: ", data);
</script>

<main>
  <h1>Worldwide Military Expenditure 2022</h1>
  <h3>In Billion US Dollars (constant 2022) and percent of total</h3>
  {#await data}
    <p>Loading..</p>
  {:then value}
    <Chart data={value} />
  {/await}
  <div class="source">
    <strong>Data:</strong>
    <a href="https://www.sipri.org/databases/milex"
      >SIPRI Military Expenditure Database</a
    >
    | <strong>Graph:</strong>
    <a href="https://yotka.org">Jan Kühn (yotka.org)</a>
  </div>
</main>

<style>
  main {
    font-family: Lato, sans-serif;
    text-align: center;
    background-color: #f9f9f9;
    padding: 1em;
  }
  h1 {
    font-size: 2.5em;
    font-weight: 700;
    margin-bottom: 0.3em;
  }
  h3 {
    font-size: 1.3em;
    font-weight: 400;
    margin-bottom: 1em;
  }
  .source {
    font-size: 0.8em;
    margin-top: 1em;
  }
  .source a {
    color: #333;
    text-decoration: none;
    margin-top: -1em;
  }
</style>
