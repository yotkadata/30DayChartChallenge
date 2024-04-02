<script>
  // Code adapted from
  // https://codesandbox.io/s/donation-prototypes-voronoi-treemap-znnql
  // By https://codesandbox.io/u/sabrinamochi

  import { hierarchy, range, scaleOrdinal } from "d3";
  import { nest } from "d3-collection";
  import { voronoiTreemap } from "d3-voronoi-treemap";

  export let data;
  export let colorsDiverging;

  const nested = nest()
    .key((d) => d.group)
    .entries(data);

  const hier = hierarchy({ key: "group", values: nested }, (d) => d.values).sum(
    (d) => +d.expenditure
  );

  // Dimensions
  let chartSize = 700;
  let margin = { top: 0, right: 0, bottom: 0, left: 0 };

  // Colors
  const groupTypes = [...new Set(data.map((d) => d.group))];
  const colorScale = scaleOrdinal().domain(groupTypes).range([
    colorsDiverging[9], // NATO
    colorsDiverging[6], // Others
  ]);
  const specialColors = {
    China: colorsDiverging[0],
    India: colorsDiverging[1],
    "Saudi Arabia": colorsDiverging[2],
    Russia: colorsDiverging[3],
    USA: colorsDiverging[10],
  };

  console.log("Color Scale: ", specialColors);

  function colorHierarchy(h) {
    if (h.depth === 0) {
      h.color = "none";
    } else if (h.depth === 1) {
      h.color = colorScale(h.data.key);
    } else {
      h.color = h.parent.color;
    }
    if (h.children) {
      h.children.forEach((child) => colorHierarchy(child));
    }
  }

  // Build chart
  const ellipse = range(100).map((i) => [
    (chartSize * (1 + 0.99 * Math.cos((i / 50) * Math.PI))) / 2,
    (chartSize * (1 + 0.99 * Math.sin((i / 50) * Math.PI))) / 2,
  ]);
  const voronoiTreeMap = voronoiTreemap().clip(ellipse);

  colorHierarchy(hier);
  voronoiTreeMap(hier);

  let allNodes = hier
    .descendants()
    .sort((a, b) => b.depth - a.depth)
    .map((d, i) => Object.assign({}, d, { id: i }));
</script>

<div
  class="chart"
  bind:clientWidth={chartSize}
  style="width: {chartSize + margin.left + margin.right}px; height: {chartSize +
    margin.top +
    margin.bottom}px; margin: 0 auto;"
>
  <svg width={chartSize} height={chartSize}>
    <g transform={`translate(${margin.left}, ${margin.top})`}>
      <g>
        {#each allNodes as node}
          <path
            d={"M" + node.polygon.join("L") + "Z"}
            fill={specialColors[node.data.country] ||
              (node.parent ? node.parent.color : node.color)}
            stroke={node.data.group === "NATO" ? "white" : "black"}
          />
          {#if node.polygon.site && node.data.pct_of_total > 0.021}
            <g
              transform={`translate(${node.polygon.site.x}, ${node.polygon.site.y - 5})`}
            >
              <text
                x={0}
                y={0}
                text-anchor="middle"
                dominant-baseline="middle"
                fill={node.data.group === "NATO" ||
                node.data.country === "China"
                  ? "white"
                  : "black"}
                font-size="14px"
                font-weight="bold"
              >
                {node.data.country}
              </text>
              <text
                x={0}
                y={15}
                text-anchor="middle"
                dominant-baseline="middle"
                fill={node.data.group === "NATO" ||
                node.data.country === "China"
                  ? "white"
                  : "black"}
                font-size="14px"
              >
                {(node.value / 1_000_000_000).toLocaleString("en-US", {
                  style: "currency",
                  currency: "USD",
                  minimumFractionDigits: 0,
                  maximumFractionDigits: 0,
                })}B, {node.data.pct_of_total.toLocaleString("en-US", {
                  style: "percent",
                  minimumFractionDigits: 1,
                  maximumFractionDigits: 1,
                })}
              </text>
            </g>
          {/if}
        {/each}
      </g>
    </g>
  </svg>
</div>

<style>
</style>
