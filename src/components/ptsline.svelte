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
    d3.csv("DSC106_NBA.csv").then((csvData) => {
      data = csvData;
      let tempData = cloneDeep(data);
      for (let yr_data of tempData) {
        let toAdd = {};
        toAdd["year"] = parseInt(yr_data.Year);
        var yr = yr_data.Year;
        delete yr_data.Year;
        toAdd["avg"] = takeAverage(yr, yr_data);
        year_avg[counter] = toAdd;
        counter++;
      }
      updateTeamPts();
      renderLinePlot(year_avg);
      checkbox(options);
    });
  });
  function takeAverage(y, d) {
    var vals = Object.values(d)
      .map((pts) => parseFloat(pts))
      .filter((value) => value > 0);
    let sum = vals.reduce(
      (accumulator, currentValue) => accumulator + currentValue,
      0,
    );
    let average = sum / vals.length;
    return parseFloat(average.toFixed(1));
  }
  function updateTeamPts() {
    team_pts = {};
    let year_data = cloneDeep(data);
    for (let tm of team_line) {
      counter = 0;
      team_pts[tm] = [];
      for (let yer of year_data) {
        if (parseFloat(yer[tm]) > 0) {
          var toadd = { year: parseInt(yer.Year), pts: parseFloat(yer[tm]) };
          team_pts[tm][counter] = toadd;
          counter++;
        }
      }
    }
  }
  function renderLinePlot(dataArray) {
    const margin = { top: 40, right: 120, bottom: 150, left: 60 };
    const width = 1400 - margin.left - margin.right;
    height = 600 - margin.top - margin.bottom;

    let minimumX = Math.min(...year_avg.map((obj) => parseInt(obj["year"])));
    let maximumX = Math.max(...year_avg.map((obj) => parseInt(obj["year"])));
    let minimumY = 0;
    let maximumY = Math.max(...year_avg.map((obj) => obj["avg"])) + 20;

    xLine = d3.scaleLinear().domain([minimumX, maximumX]).range([0, width]);

    yLine = d3.scaleLinear().domain([minimumY, maximumY]).range([height, 0]);

    line = d3
      .select("#linechart")
      .append("svg")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
      .append("g")
      .attr("transform", `translate(${margin.left},${margin.top})`);
    line.exit().remove();

    line
      .append("g")
      .attr("transform", `translate(0,${height})`)
      .call(d3.axisBottom(xLine));

    line.append("g").call(d3.axisLeft(yLine));

    line
      .selectAll("lines")
      .data([year_avg])
      .enter()
      .append("path")
      .attr(
        "d",
        d3
          .line()
          .x(function (d) {
            return xLine(d.year);
          })
          .y(function (d) {
            return yLine(d.avg);
          })
          .curve(d3.curveBasis),
      )
      .attr("class", "line")
      .attr("fill", "none")
      .attr("stroke", "red")
      .attr("stroke-width", 2.5);
      line
      .append("text")
      .attr("transform", "rotate(-90)")
      .attr("y", 0 - margin.left) // Adjust the position as needed
      .attr("x", 0 - height / 2)
      .attr("dy", "1em")
      .style("text-anchor", "middle")
      .text("Average Points Per Game")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");

    line
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
  function updateLine(dataArray) {
    if (team_line.length != 0) {
      u = line.selectAll(".line").data(Object.values(team_pts));

      u.enter()
        .append("path")
        .attr("class", "line")
        .merge(u)
        .transition()
        .duration(1000)
        .attr(
          "d",
          d3
            .line()
            .x(function (d) {
              return xLine(d.year);
            })
            .y(function (d) {
              return yLine(d.pts);
            })
            .curve(d3.curveBasis),
        )
        .attr("fill", "none")
        .attr("stroke", (d, i) => colors[i])
        .attr("stroke-width", 2.5);
      u.exit().remove();
      updateLegend(team_line);
    } else {
      renderYearAvg();
      originalLegend();
    }

    function renderYearAvg() {
      u = line.selectAll(".line").data([year_avg]);
      u.enter()
        .append("path")
        .attr("class", "line")
        .merge(u)
        .transition()
        .duration(1000)
        .attr(
          "d",
          d3
            .line()
            .x(function (d) {
              return xLine(d.year);
            })
            .y(function (d) {
              return yLine(d.avg);
            })
            .curve(d3.curveBasis),
        )
        .attr("fill", "none")
        .attr("stroke", (d, i) => colors[i])
        .attr("stroke-width", 2.5);
      u.exit().remove();
    }
  }

  function checkbox(data) {
    select = d3
      .select("#body")
      .append("select")
      .attr("multiple", true)
      .attr("size", 8);
    select
      .selectAll("option")
      .data(data)
      .enter()
      .append("option")
      .attr("value", (d) => d)
      .text((d) => d);

    select.on("change", function () {
      let options = this.selectedOptions;
      team_line = Array.from(options).map((option) => option.value);
      updateTeamPts();
      updateLine(team_pts);
    });
  }

  function updateLegend(teams) {
    // Remove existing legend items
    d3.select("#legend").selectAll("*").remove();
    if (teams.length != 0) {
      let legendItems = d3
        .select("#legend")
        .selectAll("div")
        .data(teams)
        .enter()
        .append("div")
        .attr("class", "legend-item");

      // Add colored rectangles (squares) to represent each team
      legendItems
        .append("div")
        .attr("class", "legend-color")
        .style("background-color", (d, i) => colors[i]) // Assign colors based on the colors array
        .style("width", "20px") // Adjust the width of the rectangle as needed
        .style("height", "20px"); // Assign colors based on the colors array

      // Add text for each team
      legendItems
        .append("div")
        .text((d) => d)
        .attr("class", "legend-text")
        .style("margin-right", "20px");
    }
  }

  function originalLegend() {
    d3.select("#legend").selectAll("*").remove();
    if (teams.length != 0) {
      let legendItem = d3.select("#legend").attr("class", "legend-item");

      // Add colored rectangles (squares) to represent each team
      legendItem
        .append("div")
        .attr("class", "legend-color")
        .style("background-color", colors[0]) // Assign colors based on the colors array
        .style("width", "20px") // Adjust the width of the rectangle as needed
        .style("height", "20px"); // Assign colors based on the colors array

      // Add text for each team
      legendItem
        .append("div")
        .text("League Average")
        .attr("class", "legend-text")
        .style("margin-right", "20px");
    }
  }
</script>

<main>
  <div id="highlightable-box" class="highlightable-box">
    <!-- Highlightable elements (team names) will be rendered here -->
  </div>
  <div id="body">
    <h2>Select teams to view:</h2>
    <p>
      You can select multiple teams by either click and drag or by holding
      command/ctrl and clicking on the desired teams
    </p>
    <div id="legend" class="legend-container"></div>
  </div>
  <div id="linechart" class="chart_class">
    <h2>Average Points Per Game over the Years</h2>
  </div>

  <div class="highlightable-box">
    <h2 class="centered_title">Conclusion</h2>
  </div>
  <div class="paragraph_annotation">
    <p>
      NBA teams are scoring more and more points recently AND many fans are
      worried. BUT these fans have most likely only been watching since 90s and
      2000s and have therefore only witnessed an increase in scoring in their
      lifetimes. THEREFORE the current levels of scoring and the state of the
      game are not necessarily things to be worried about as we have seen
      similar levels of scoring in the past.
    </p>
  </div>
  <div class="highlightable-box">
    <h2 class="centered_title">
      Possible Explanation for Recent Scoring Spike
    </h2>
  </div>
  <div class="paragraph_annotation">
    <p>
      There has been a recent explosion in the usage of the three point shot,
      with the average number of three point attempts going from 18 in 2011 to
      nearly doubling in the 2021 with 35.2. With more three pointers being
      attempted and teams greatly valuing high percentage three point shooters,
      expected scoring totals greatly increase. This is coupled with a rebound
      in the pace of play from the molasses marred speed of the 90s and 2000s to
      the more uptempo style of the 70s and 80s. For context, the slowest paced
      team this past season was still faster than the fastest team in 2001. The
      combination of these two phenomena is a possible cause for recent uptick.
    </p>
    <p>Below are two graphs that help visualize the phenomena.</p>
  </div>
</main>
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

  .NBA_img {
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

  .highlightable-box,
  #body,
  #body3pt,
  #demo_title,
  iframe {
    margin-left: 40px;
    margin-right: 40px;
  }
  .container {
    display: flex;
    align-items: center; /* Vertically center items */
    margin-bottom: 45px;
    margin-top: 25px;
  }

  .text_test {
    margin-left: 30px; /* Adjust spacing between image and text */
    margin-right: 30px;
    width: 400px;
    font-size: 18px;
    text-align: justify;
  }

  #first_hook,
  #second_hook {
    height: 250px;
  }

  #linechart,
  #linechart2 {
    margin-top: -100px;
    margin-bottom: -50px;
  }
  .centered_title {
    text-align: center;
  }
  iframe {
    display: flex;
    justify-content: center;
    align-items: center;
  }
  #vid {
    display: flex;
    justify-content: center;
    align-items: center;
  }
  #legend {
    display: flex; /* Display legend items in a flex container */
    flex-wrap: wrap; /* Allow legend items to wrap to next line if needed */
  }
  .legend-container {
    display: flex; /* Display legend items in a flex container */
    flex-wrap: wrap; /* Allow legend items to wrap to next line if needed */
  }

  .legend-item {
    margin-right: 20px; /* Adjust margin between legend items */
  }

  .legend-color {
    display: inline-block; /* Display colored rectangles inline */
    margin-right: 5px; /* Adjust margin between colored rectangles and text */
  }
</style>
