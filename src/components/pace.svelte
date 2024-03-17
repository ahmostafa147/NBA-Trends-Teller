<script>
  import { onMount } from "svelte";
  import * as d3 from "d3";
  import cloneDeep from "lodash/cloneDeep";
  let data = [];
  let keys = [];
  let teams = [];
  let values = [];
  let year = 1950;
  let interval;
  let playing = false;
  let minVal = 70;
  let maxVal = 130;
  let svg;
  let svg1;
  let svg2;
  let svg3;
  let svg5;
  let select;
  let select2;
  let xLine;
  let yLine;
  let xLine2;
  let yLine2;
  let line3;
  let xLine3;
  let yLine3;
  let u;
  let u3;
  let x;
  let y;
  let bars;
  let height;
  let logos;
  let team_line = [];
  let team_3pt_line = [];
  let year_avg = [];
  let year_three_avg = [];
  var counter = 0;
  let team_pts = {};
  let team_3pts = {};
  let threepointData;
  let tooltip;
  let line;
  let linePace;
  let paceData;
  const colors = [
    "#1f77b4",
    "#ff7f0e",
    "#2ca02c",
    "#d62728",
    "#9467bd",
    "#8c564b",
    "#e377c2",
    "#7f7f7f",
    "#bcbd22",
    "#17becf",
    "#aec7e8",
    "#ffbb78",
    "#98df8a",
    "#ff9896",
    "#c5b0d5",
    "#c49c94",
    "#f7b6d2",
    "#c7c7c7",
    "#dbdb8d",
    "#9edae5",
    "#393b79",
    "#ff9896",
    "#2ca02c",
    "#d62728",
    "#9467bd",
    "#8c564b",
    "#1f77b4",
    "#7f7f7f",
    "#bcbd22",
    "#17becf",
    "#aec7e8",
  ];

  const options = [
    "Boston Celtics",
    "New York Knicks",
    "Golden State Warriors",
    "Sacramento Kings",
    "Los Angeles Lakers",
    "Detroit Pistons",
    "Philadelphia 76ers",
    "Atlanta Hawks",
    "Washington Wizards",
    "Chicago Bulls",
    "Houston Rockets",
    "Oklahoma City Thunder",
    "Brooklyn Nets",
    "Denver Nuggets",
    "Indiana Pacers",
    "San Antonio Spurs",
    "Milwaukee Bucks",
    "Phoenix Suns",
    "Cleveland Cavaliers",
    "Los Angeles Clippers",
    "Portland Trail Blazers",
    "Utah Jazz",
    "Dallas Mavericks",
    "Miami Heat",
    "Orlando Magic",
    "Minnesota Timberwolves",
    "Charlotte Hornets",
    "Memphis Grizzlies",
    "Toronto Raptors",
    "New Orleans Pelicans",
  ];

  const teamLogos = {
    "Atlanta Hawks": "modeling_plot/src/components/logos/atlanta hawks.svg",
    "Boston Celtics":
      "https://cdn.nba.com/logos/nba/1610612738/primary/L/logo.svg",
    "Brooklyn Nets":
      "https://cdn.nba.com/logos/nba/1610612751/primary/L/logo.svg",
    "New York Knicks":
      "https://cdn.nba.com/logos/nba/1610612752/primary/L/logo.svg",
    "Philadelphia 76ers":
      "https://cdn.nba.com/logos/nba/1610612755/primary/L/logo.svg",
    "Toronto Raptors":
      "https://cdn.nba.com/logos/nba/1610612761/primary/L/logo.svg",
    "Chicago Bulls":
      "https://cdn.nba.com/logos/nba/1610612741/primary/L/logo.svg",
    "Cleveland Cavaliers":
      "https://cdn.nba.com/logos/nba/1610612739/primary/L/logo.svg",
    "Detroit Pistons":
      "https://cdn.nba.com/logos/nba/1610612765/primary/L/logo.svg",
    "Indiana Pacers":
      "https://cdn.nba.com/logos/nba/1610612754/primary/L/logo.svg",
    "Milwaukee Bucks":
      "https://cdn.nba.com/logos/nba/1610612749/primary/L/logo.svg",
    "Atlanta Hawks":
      "https://cdn.nba.com/logos/nba/1610612737/primary/L/logo.svg",
    "Charlotte Hornets":
      "https://cdn.nba.com/logos/nba/1610612766/primary/L/logo.svg",
    "Miami Heat": "https://cdn.nba.com/logos/nba/1610612748/primary/L/logo.svg",
    "Orlando Magic":
      "https://cdn.nba.com/logos/nba/1610612753/primary/L/logo.svg",
    "Washington Wizards":
      "https://cdn.nba.com/logos/nba/1610612764/primary/L/logo.svg",
    "Denver Nuggets":
      "https://cdn.nba.com/logos/nba/1610612743/primary/L/logo.svg",
    "Minnesota Timberwolves":
      "https://cdn.nba.com/logos/nba/1610612750/primary/L/logo.svg",
    "Oklahoma City Thunder":
      "https://cdn.nba.com/logos/nba/1610612760/primary/L/logo.svg",
    "Portland Trail Blazers":
      "https://cdn.nba.com/logos/nba/1610612757/primary/L/logo.svg",
    "Utah Jazz": "https://cdn.nba.com/logos/nba/1610612762/primary/L/logo.svg",
    "Golden State Warriors":
      "https://cdn.nba.com/logos/nba/1610612744/primary/L/logo.svg",
    "Los Angeles Clippers":
      "https://cdn.nba.com/logos/nba/1610612746/primary/L/logo.svg",
    "Los Angeles Lakers":
      "https://cdn.nba.com/logos/nba/1610612747/primary/L/logo.svg",
    "Phoenix Suns":
      "https://cdn.nba.com/logos/nba/1610612756/primary/L/logo.svg",
    "Sacramento Kings":
      "https://cdn.nba.com/logos/nba/1610612758/primary/L/logo.svg",
    "Dallas Mavericks":
      "https://cdn.nba.com/logos/nba/1610612742/primary/L/logo.svg",
    "Houston Rockets":
      "https://cdn.nba.com/logos/nba/1610612745/primary/L/logo.svg",
    "Memphis Grizzlies":
      "https://cdn.nba.com/logos/nba/1610612763/primary/L/logo.svg",
    "New Orleans Pelicans":
      "https://cdn.nba.com/logos/nba/1610612740/primary/L/logo.svg",
    "San Antonio Spurs":
      "https://cdn.nba.com/logos/nba/1610612759/primary/L/logo.svg",
  };

  onMount(() => {
    d3.csv("NBA_PACE.csv").then((csvdata) => {
      paceData = csvdata;
      renderLinePlotPace();
    });
  });

  function renderLinePlotPace() {
    const margin = { top: 40, right: 120, bottom: 150, left: 60 };
    const width = 1400 - margin.left - margin.right;
    height = 600 - margin.top - margin.bottom;
    let minimumX = Math.min(...paceData.map((obj) => parseInt(obj["Year"])));
    let maximumX = Math.max(...paceData.map((obj) => parseInt(obj["Year"])));
    let minimumY = 0;
    let maximumY = Math.max(...paceData.map((obj) => obj["Pace"])) + 20;

    xLine2 = d3.scaleLinear().domain([minimumX, maximumX]).range([0, width]);

    yLine2 = d3.scaleLinear().domain([minimumY, maximumY]).range([height, 0]);

    linePace = d3
      .select("#linechartpace")
      .append("svg")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
      .append("g")
      .attr("transform", `translate(${margin.left},${margin.top})`);

    linePace
      .append("g")
      .attr("transform", `translate(0,${height})`)
      .call(d3.axisBottom(xLine2));

    linePace.append("g").call(d3.axisLeft(yLine2));

    linePace
      .selectAll("lines")
      .data([paceData])
      .enter()
      .append("path")
      .attr(
        "d",
        d3
          .line()
          .x(function (d) {
            return xLine2(d.Year);
          })
          .y(function (d) {
            return yLine2(d.Pace);
          })
          .curve(d3.curveBasis),
      )
      .attr("class", "linepace")
      .attr("fill", "none")
      .attr("stroke", "red")
      .attr("stroke-width", 2.5);
    linePace
      .append("text")
      .attr("transform", "rotate(-90)")
      .attr("y", 0 - margin.left) // Adjust the position as needed
      .attr("x", 0 - height / 2)
      .attr("dy", "1em")
      .style("text-anchor", "middle")
      .text("Average Pace Per Game")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");

    linePace
      .append("text")
      .attr("transform", `translate(${width / 2}, ${height + 120})`) // Adjust the position as needed
      .style("text-anchor", "middle")
      .text("Year")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");
  }
</script>

<main></main>

<style>
  #overlay {
    font-size: 0.9em;
    /* position: absolute; */
    margin-left: auto;
    margin-right: 0;
    min-width: 250px;
    width: 15%;
    top: 180px;
    right: 10px;
    padding: 10px;
    z-index: 3;
  }

  input {
    display: inline-block;
    width: 100%;
    position: relative;
    margin: 0;
    cursor: pointer;
  }

  label {
    font-size: 1.5em;
    font-family: sans-serif;
    font-weight: bold;
  }

  .chart_class,
  #title {
    background-color: antiquewhite;
    margin: auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
  }

  h2 {
    font-size: 24px;
    font-family: Impact, Haettenschweiler, "Arial Narrow Bold", sans-serif;
  }

  h1 {
    font-size: 36px;
    font-family: Impact, Haettenschweiler, "Arial Narrow Bold", sans-serif;
  }

  img {
    position: absolute;
    /* margin-right: auto; 
      margin-left: 0; */
    top: 0;
    left: 0;
    height: 20%;
  }

  #text,
  .paragraph_annotation {
    font-size: 18px;
    margin-left: 40px;
    margin-right: 40px;
    text-align: justify;
    font-family: Arial, Helvetica, sans-serif;
  }
</style>
