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
        "Miami Heat":
            "https://cdn.nba.com/logos/nba/1610612748/primary/L/logo.svg",
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
        "Utah Jazz":
            "https://cdn.nba.com/logos/nba/1610612762/primary/L/logo.svg",
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
            renderBarChart3(1980);
        });
    });

    function renderBarChart3(year) {
    teams = data.filter((d) => d.Year == year);
    keys = Object.keys(teams[0]).filter((key) => key !== "Year");
    values = Object.entries(teams[0])
      .filter((entry) => entry[0] !== "Year")
      .map(([key, value]) => {
        const logo = teamLogos[key];
        return {
          team: key,
          score: value - minVal,
          logo:
            logo ||
            "https://cdn.freebiesupply.com/images/large/2x/nba-logo-transparent.png",
        }; // Provide a default logo URL if not found
      });

    const margin = { top: 40, right: 120, bottom: 150, left: 60 };
    const width = 1400 - margin.left - margin.right;
    height = 600 - margin.top - margin.bottom;

    svg2 = d3
      .select("#chart3")
      .append("svg")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
      .append("g")
      .attr("transform", `translate(${margin.left},${margin.top})`);

    x = d3.scaleBand().domain(keys).range([0, width]).padding(0.1);

    y = d3.scaleLinear().domain([minVal - minVal, maxVal - minVal]).range([height, 0]);

    svg2
      .append("g")
      .attr("transform", `translate(0,${height})`)
      .call(d3.axisBottom(x))
      .selectAll("text")
      .attr("transform", "rotate(-45)")
      .style("text-anchor", "end")
      .style("font-size", "12px");

    svg2.append("g").call(d3.axisLeft(y));

    svg2
      .selectAll(".logo")
      .data(values)
      .enter()
      .append("svg:image")
      .attr("xlink:href", (d) => d.logo)
      .attr("x", (d) => x(d.team) + x.bandwidth() / 2 - 15) // Adjust positioning as needed
      .attr("y", (d) => y(d.score) - 40) // Adjust positioning as needed
      .attr("width", 30)
      .attr("height", 30)
      .attr("class", "logo");

    svg2
      .selectAll("rect")
      .data(values)
      .enter()
      .append("rect")
      .attr("x", (d) => x(d.team))
      .attr("y", (d) => y(d.score))
      .attr("width", x.bandwidth())
      .attr("height", (d) => height - y(d.score))
      .attr("fill", "#FA8320")
      .attr("stroke", "black")
      .attr("stroke-width", 2)
      .on("mouseover", (event, d) => {
        const tooltipWidth = 120;
        const tooltipHeight = 60;

        const tooltip = svg2
          .append("g")
          .attr("class", "tooltip")
          .attr(
            "transform",
            `translate(${x(d.team) + x.bandwidth()}, ${y(d.score)})`,
          );

        tooltip
          .append("rect")
          .attr("width", tooltipWidth)
          .attr("height", tooltipHeight)
          .attr("fill", "rgba(255, 255, 255, 0.8)")
          .attr("stroke", "black")
          .attr("stroke-width", 1);

        tooltip
          .append("text")
          .attr("x", tooltipWidth / 2)
          .attr("y", tooltipHeight / 2 - 10)
          .attr("dy", "0.35em")
          .attr("text-anchor", "middle")
          .style("font-size", "12px")
          .text(`${d.team}`);

        tooltip
          .append("text")
          .attr("x", tooltipWidth / 2)
          .attr("y", tooltipHeight / 2 + 10)
          .attr("dy", "0.35em")
          .attr("text-anchor", "middle")
          .style("font-size", "12px")
          .text(`PPG: ${d.score}`);
        updateTooltipPosition(event, d, svg2);
      })
      .on("mousemove", (event, d) => {
        updateTooltipPosition(event, d, svg2);
      })
      .on("mouseout", (event) => {
        svg2.select(".tooltip").remove();
      });

    svg2
      .append("text")
      .attr("transform", `translate(${width / 2}, ${height + 120})`) // Adjust the position as needed
      .style("text-anchor", "middle")
      .text("Teams")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");

    svg2
      .append("text")
      .attr("transform", "rotate(-90)")
      .attr("y", 0 - margin.left) // Adjust the position as needed
      .attr("x", 0 - height / 2)
      .attr("dy", "1em")
      .style("text-anchor", "middle")
      .text("Average Points Per Game Differece")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");

    const lineData1 = [
      { x: 0, y: 150 },
      { x: 1220, y: 150 },
    ];
    const lineFunction = d3
      .line()
      .x((d) => d.x)
      .y((d) => d.y);

    svg2
      .append("path")
      .attr("d", lineFunction(lineData1))
      .attr("stroke", "red")
      .attr("stroke-width", 2)
      .attr("fill", "none");

    const lineData2 = [
      { x: 805, y: 150 },
      { x: 890, y: 85 },
    ];

    svg2
      .append("path")
      .attr("d", lineFunction(lineData2))
      .attr("stroke", "gray")
      .attr("stroke-width", 2)
      .attr("fill", "none");

    const lineData3 = [
      { x: 890, y: 85 },
      { x: 1250, y: 85 },
    ];

    svg2
      .append("path")
      .attr("d", lineFunction(lineData3))
      .attr("stroke", "gray")
      .attr("stroke-width", 2)
      .attr("fill", "none");

    const foreignObject = svg2
      .append("foreignObject")
      .attr("x", 890)
      .attr("y", 15)
      .attr("width", 350)
      .attr("height", 100);

    const div = foreignObject
      .append("xhtml:div")
      .style("font-size", "14px")
      .style("color", "black")
      .style("text-align", "justify");

    div
      .html(
        "The league scoring average in the 1980 NBA season was 108.1. \
            This was a typical season in the 70s and 80s, an era dominated by \
            stars like Kareem Abdul-Jabbar, Magic Johnson, and Larry Bird.",
      )
      .style("font-size", "14px");
     
    const foreignObject2 = svg2
      .append("foreignObject")
      .attr("x", 1044)
      .attr("y", 90)
      .attr("width", 350)
      .attr("height", 500);

    const div2 = foreignObject2
      .append("xhtml:div")
      .style("font-size", "14px")
      .style("color", "black")
      .style("text-align", "justify");

    div2
      .append("img")
      .attr(
        "src",
        "https://www.usatoday.com/gcdn/authoring/2019/05/15/USAT/af4479ee-4fb3-427d-8b0a-13034cf41d25-XXX_IMG_XXX_IMG_XXX_LARRY_BI_1_1_USIPMVCK.JPG",
      )
      // .attr("width", 500); // Adjust the width as needed
      .attr("height", 310);
  
  }
  function updateTooltipPosition(event, d, svg) {
        tooltip = svg.select(".tooltip");
        const tooltipWidth = 100;
        const tooltipHeight = 40;
        // const mouseX = event.pageX;
        // const mouseY = event.pageY;

        // tooltip.attr(
        //   "transform",
        //   `translate(${mouseX - 280}, ${mouseY - tooltipHeight - 170})`,
        // );
        // tooltip.attr(
        //   "transform",
        //   `translate(${0.5 * mouseX}, ${0.5 * mouseY})`,
        // );
        // console.log(event.toElement);
        let [mouseX, mouseY] = d3.pointer(event);
        // console.log(mouseX, mouseY);
        tooltip.attr(
            "transform",
            `translate(${mouseX + 10}, ${mouseY - tooltipHeight + 5})`,
        );
        // tooltip.select('.text').text(`${event.srcElement.__data__.team}: ${event.srcElement.__data__.score} dsfjhsdk`);
        // console.log(tooltip.select('.text'))
        // console.log(event.srcElement.__data__);
        // console.log(tooltip.selectAll('text'));
        // const first = tooltip.select('text')
        // tooltip.select('text').text(`${d.team}: ${d.score}`);
        tooltip
            .select("text:nth-child(3)")
            .text(`PPG Diff: ${Math.round(d.score * 100) / 100}`);
        // tooltip.select("text:nth-child(3)").text(`PPG: ${d.score}`);
        // tooltip.selectAll('text').text(`${d.team}: ${d.score}`);
    }
</script>

<main>
    <div id="chart3" class="chart_class">
        <h2 style="text-align: left;">
          NBA Teams Difference in Average Points per Game in 1980 From All Time
          Lowest Average
        </h2>
      </div>
      <div class="paragraph_annotation">
        <p>We now move forward to 1980. The NBA is thriving in this era, with the
          Showtime Lakers and the Magic versus Bird rivalry captivating fans. This
          year, the league had a scoring average of 108.1 points per game, which was
          pretty typical of the 70s and 80s. Trail blazed by the Showtime Lakers,
          teams were playing at an unprecedented pace. The three-point line was also
          introduced in 1979, allowing another avenue of elevated scoring although
          teams were not quick to prioritize it.</p>
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
  
    .chart_class, #title {
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
  
    #text, .paragraph_annotation {
      font-size: 18px;
      margin-left: 40px;
      margin-right: 40px;
      text-align: justify;
      font-family: Arial, Helvetica, sans-serif;
    }
  </style>
  