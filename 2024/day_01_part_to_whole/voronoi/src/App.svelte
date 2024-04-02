<script>
  // Code adapted from
  // https://codesandbox.io/s/donation-prototypes-voronoi-treemap-znnql
  // By https://codesandbox.io/u/sabrinamochi

  import Chart from "./Chart.svelte";

  const dataPath = "./src/data/sipri_voronoi.json";
  async function loadData(path) {
    const raw = await fetch(path);
    const json = await raw.json();
    return json;
  }
  let data = loadData(dataPath);

  const colorsDiverging = [
    "#580000",
    "#a54e18",
    "#dd892d",
    "#efc68a",
    "#faeec7",
    "#ffffe0",
    "#e7f4d9",
    "#acdac7",
    "#56b3ae",
    "#267978",
    "#003233",
  ];
</script>

<main>
  <h1>NATO Dominates Military Expenditure</h1>
  <h3>
    In 2022, <strong style="color: {colorsDiverging[9]}; font-weight: 900;"
      >NATO's</strong
    >
    30 members made up over 54% of world military spending, with the
    <strong style="color: {colorsDiverging[10]}; font-weight: 900;">USA</strong>
    outspending the next eight countries combined. Following were
    <strong style="color: {colorsDiverging[0]}; font-weight: 900;">China</strong
    >,
    <strong style="color: {colorsDiverging[1]}; font-weight: 900;">India</strong
    >, and
    <strong style="color: {colorsDiverging[2]}; font-weight: 900;"
      >Saudi Arabia</strong
    >.
    <strong style="color: {colorsDiverging[3]}; font-weight: 900;"
      >Russia</strong
    >, who started its war on Ukraine in 2022, ranked fifth at that point.
  </h3>
  {#await data}
    <p>Loading..</p>
  {:then value}
    <Chart data={value} {colorsDiverging} />
  {/await}
  <div class="source">
    <p>
      Numbers are in Billion US Dollars (in constant 2022 prices) and as percent
      of total global military spending.
    </p>
    <p>
      <strong>Data:</strong>
      <a href="https://www.sipri.org/databases/milex"
        >SIPRI Military Expenditure Database</a
      >
      | <strong>Graph:</strong>
      <a href="https://yotka.org">Jan Kühn (yotka.org)</a>
    </p>
  </div>
</main>

<style>
  main {
    font-family: Lato, sans-serif;
    text-align: center;
    background-color: #f9f9f9;
    padding: 1em;
    max-width: 800px;
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
    margin-top: 2em;
  }
  .source p {
    color: #333;
    font-size: 0.8em;
    margin: 0.5em 0;
  }
  .source a {
    color: #333;
    text-decoration: none;
    margin-top: -1em;
  }
</style>
