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
    d3.csv("src/NBA_PACE.csv").then((csvdata) => {
      paceData = csvdata;
    })
    d3.csv("src/NBA_3PA.csv").then((csvdata) => {
      threepointData = csvdata;
    })
    d3.csv("src/DSC106_NBA.csv").then((csvData) => {
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
      renderBarChart(year);
      renderBarChart2(1953);
      renderBarChart3(1980);
      renderBarChart4(1998);
      renderBarChart5(2022);
      updateTeamPts();
      renderLinePlot(year_avg);
      renderLinePlotPace();
      checkbox(options);
      let threeData = cloneDeep(threepointData);
      counter=0
      for (let yr_data of threeData) {
        let toAdd3 = {};
        toAdd3["year"] = parseInt(yr_data.Year);
        var yr3pt = yr_data.Year;
        delete yr_data.Year;
        toAdd3["avg"] = takeAverage(yr3pt, yr_data);
        year_three_avg[counter] = toAdd3;
        counter++;
      }
      updateTeam3Pts();
      renderLinePlotThree(year_three_avg);
      checkbox3pt(options);
    });
  });

  function renderBarChart(year) {
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

    svg = d3
      .select("#chart")
      .append("svg")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
      .append("g")
      .attr("transform", `translate(${margin.left},${margin.top})`);

    x = d3.scaleBand().domain(keys).range([0, width]).padding(0.1);

    y = d3.scaleLinear().domain([minVal - minVal, maxVal - minVal]).range([height, 0]);

    svg
      .append("g")
      .attr("transform", `translate(0,${height})`)
      .call(d3.axisBottom(x))
      .selectAll("text")
      .attr("transform", "rotate(-45)")
      .style("text-anchor", "end")
      .style("font-size", "12px");

    svg.append("g").call(d3.axisLeft(y));

    svg
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

    svg
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

        const tooltip = svg
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
          .text(`PPG Diff: ${d.score}`);
      })
      .on("mousemove", (event, d) => {
        updateTooltipPosition(event, d, svg);
      })
      .on("mouseout", (event) => {
        svg.select(".tooltip").remove();
      });

    svg
      .append("text")
      .attr("transform", `translate(${width / 2}, ${height + 120})`) // Adjust the position as needed
      .style("text-anchor", "middle")
      .text("Teams")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");

    svg
      .append("text")
      .attr("transform", "rotate(-90)")
      .attr("y", 0 - margin.left) // Adjust the position as needed
      .attr("x", 0 - height / 2)
      .attr("dy", "1em")
      .style("text-anchor", "middle")
      .text("Average Points Per Game Difference")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");
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
    tooltip.select("text:nth-child(3)").text(`PPG Diff: ${Math.round(d.score * 100) / 100}`);
    // tooltip.select("text:nth-child(3)").text(`PPG: ${d.score}`);
    // tooltip.selectAll('text').text(`${d.team}: ${d.score}`);
  }

  function updateBars(newyr) {
    teams = data.filter((d) => d.Year == newyr);
    keys = Object.keys(teams[0]).filter((key) => key !== "Year");
    values = Object.entries(teams[0])
      .filter((entry) => entry[0] !== "Year")
      .map(([key, value]) => {
        return { team: key, score: value - minVal };
      });

    bars = svg.selectAll("rect").data(values);

    bars
      .enter()
      .append("rect")
      .attr("x", (d) => x(d.team))
      .attr("width", x.bandwidth())
      .merge(bars)
      .transition()
      .duration(800)
      .attr("y", (d) => y(d.score))
      .attr("height", (d) => height - y(d.score))
      .attr("fill", "#FA8320")
      .attr("stroke", "black")
      .attr("stroke-width", 2);

    logos = svg.selectAll(".logo").data(values);
    logos
      .enter()
      .append("svg:image")
      .attr("xlink:href", (d) => d.logo)
      .attr("x", (d) => x(d.team) + x.bandwidth() / 2 - 15) // Adjust positioning as needed
      .merge(logos)
      .transition()
      .duration(800)
      .attr("y", (d) => y(d.score) - 40)
      .attr("height", (d) => y(d.score) - 800)
      .attr("class", "logo")
      .on("mouseover", (event, d) => {
        tooltip = svg.append("g").attr("class", "tooltip");

        tooltip
          .append("rect")
          .attr("width", 130)
          .attr("height", 60)
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

        updateTooltipPosition(event, d, svg);
      })
      .on("mousemove", (event, d) => {
        updateTooltipPosition(event, d, svg);
      })
      .on("mouseout", (event) => {
        svg.select(".tooltip").remove();
      });

    bars.exit().remove();
  }
  function updateYear(newYear) {
    year = newYear;
    updateBars(year);
  }

  function play() {
    playing = true;
    interval = setInterval(() => {
      if (year < 2022) {
        year++;
        updateBars(year);
      } else {
        stop();
      }
    }, 1000);
  }

  function stop() {
    clearInterval(interval);
    playing = false;
  }

  // -------------------- WARNING: cooked shit below

  function renderBarChart2(year) {
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
        };
      });

    const margin = { top: 40, right: 120, bottom: 150, left: 60 };
    const width = 1400 - margin.left - margin.right;
    height = 600 - margin.top - margin.bottom;

    svg1 = d3
      .select("#chart2")
      .append("svg")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
      .append("g")
      .attr("transform", `translate(${margin.left},${margin.top})`);

    x = d3.scaleBand().domain(keys).range([0, width]).padding(0.1);

    y = d3.scaleLinear().domain([minVal - minVal, maxVal - minVal]).range([height, 0]);

    svg1
      .append("g")
      .attr("transform", `translate(0,${height})`)
      .call(d3.axisBottom(x))
      .selectAll("text")
      .attr("transform", "rotate(-45)")
      .style("text-anchor", "end")
      .style("font-size", "12px");

    svg1.append("g").call(d3.axisLeft(y));

    svg1
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

    svg1
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

        const tooltip = svg1
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
        updateTooltipPosition(event, d, svg1);
      })
      .on("mousemove", (event, d) => {
        updateTooltipPosition(event, d, svg1);
      })
      .on("mouseout", (event) => {
        svg1.select(".tooltip").remove();
      });

    svg1
      .append("text")
      .attr("transform", `translate(${width / 2}, ${height + 120})`) // Adjust the position as needed
      .style("text-anchor", "middle")
      .text("Teams")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");

    svg1
      .append("text")
      .attr("transform", "rotate(-90)")
      .attr("y", 0 - margin.left) // Adjust the position as needed
      .attr("x", 0 - height / 2)
      .attr("dy", "1em")
      .style("text-anchor", "middle")
      .text("Average Points Per Game Difference")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");

    const lineData1 = [
      { x: 305, y: 410 },
      { x: 400, y: 310 },
    ];
    const lineFunction = d3
      .line()
      .x((d) => d.x)
      .y((d) => d.y);

    svg1
      .append("path")
      .attr("d", lineFunction(lineData1))
      .attr("stroke", "gray")
      .attr("stroke-width", 2)
      .attr("fill", "none");

    const lineData2 = [
      { x: 400, y: 310 },
      { x: 750, y: 310 },
    ];

    svg1
      .append("path")
      .attr("d", lineFunction(lineData2))
      .attr("stroke", "gray")
      .attr("stroke-width", 2)
      .attr("fill", "none");

    // svg1.append("text")
    //   .attr("x", 400)
    //   .attr("y", 300)
    //   .attr("font-size", "14px")
    //   .attr("fill", "black")
    //   .text("The lowest scoring team since 1950 is the 1953 Atlanta Hawks, \
    //   averaging a paltry 70 points per game. This lowpoint is the benchmark \
    //   from which we compare all other team averages over the next decades.")
    //   .style("text-align", "justify")
    //   .style("font-size", "12px");

    const foreignObject = svg1
      .append("foreignObject")
      .attr("x", 400)
      .attr("y", 245)
      .attr("width", 350)
      .attr("height", 100);

    const div = foreignObject
      .append("xhtml:div")
      .style("font-size", "14px")
      .style("color", "black")
      .style("text-align", "justify");

    div
      .html(
        "The lowest scoring team since 1950 is the 1953 Atlanta Hawks, \
          averaging a paltry 70 points per game. This low point is the benchmark \
          from which we will compare all other team averages over the next decades.",
      )
      .style("font-size", "14px");

    const foreignObject2 = svg1
      .append("foreignObject")
      .attr("x", 770)
      .attr("y", 0)
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
        "https://cdn.nba.com/manage/2021/09/GettyImages-1835399-scaled-e1631505695651-1568x885.jpg",
      )
      // .attr("width", 500); // Adjust the width as needed
      .attr("height", 200);

    const foreignObject3 = svg1
      .append("foreignObject")
      .attr("x", 400)
      .attr("y", 75)
      .attr("width", 350)
      .attr("height", 100);

    const div3 = foreignObject3
      .append("xhtml:div")
      .style("font-size", "14px")
      .style("color", "black")
      .style("text-align", "justify");

    div3
      .html(
        "In the 1953 NBA season, the Minneapolis Lakers led by George \
            Mikan (pictured to the right) would win their last championship before \
            moving to Los Angeles.",
      )
      .style("font-size", "14px");
  }

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
      .attr("y", 20)
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

  function renderBarChart4(year) {
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

    svg3 = d3
      .select("#chart4")
      .append("svg")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
      .append("g")
      .attr("transform", `translate(${margin.left},${margin.top})`);

    x = d3.scaleBand().domain(keys).range([0, width]).padding(0.1);

    y = d3.scaleLinear().domain([minVal - minVal, maxVal - minVal]).range([height, 0]);

    svg3
      .append("g")
      .attr("transform", `translate(0,${height})`)
      .call(d3.axisBottom(x))
      .selectAll("text")
      .attr("transform", "rotate(-45)")
      .style("text-anchor", "end")
      .style("font-size", "12px");

    svg3.append("g").call(d3.axisLeft(y));

    svg3
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

    svg3
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

        const tooltip = svg3
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
        updateTooltipPosition(event, d, svg3);
      })
      .on("mousemove", (event, d) => {
        updateTooltipPosition(event, d, svg3);
      })
      .on("mouseout", (event) => {
        svg3.select(".tooltip").remove();
      });

    svg3
      .append("text")
      .attr("transform", `translate(${width / 2}, ${height + 120})`) // Adjust the position as needed
      .style("text-anchor", "middle")
      .text("Teams")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");

    svg3
      .append("text")
      .attr("transform", "rotate(-90)")
      .attr("y", 0 - margin.left) // Adjust the position as needed
      .attr("x", 0 - height / 2)
      .attr("dy", "1em")
      .style("text-anchor", "middle")
      .text("Average Points Per Game Difference")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");

    const lineData1 = [
      { x: 0, y: 263 },
      { x: 1220, y: 263 },
    ];

    const lineFunction = d3
      .line()
      .x((d) => d.x)
      .y((d) => d.y);

    svg3
      .append("path")
      .attr("d", lineFunction(lineData1))
      .attr("stroke", "red")
      .attr("stroke-width", 2)
      .attr("fill", "none");

    const lineData2 = [
      { x: 805, y: 263 },
      { x: 900, y: 163 },
    ];

    svg3
      .append("path")
      .attr("d", lineFunction(lineData2))
      .attr("stroke", "gray")
      .attr("stroke-width", 2)
      .attr("fill", "none");

    const lineData3 = [
      { x: 900, y: 163 },
      { x: 1250, y: 163 },
    ];

    svg3
      .append("path")
      .attr("d", lineFunction(lineData3))
      .attr("stroke", "gray")
      .attr("stroke-width", 2)
      .attr("fill", "none");

    svg3
      .append("text")
      .attr("x", 900)
      .attr("y", 158)
      .attr("font-size", "14px")
      .attr("fill", "black")
      .text("")
      .style("font-size", "20px");

    const foreignObject = svg3
      .append("foreignObject")
      .attr("x", 900)
      .attr("y", 82)
      .attr("width", 350)
      .attr("height", 100);

    const div = foreignObject
      .append("xhtml:div")
      .style("font-size", "14px")
      .style("color", "black")
      .style("text-align", "justify");

    div
      .html(
        "The lowpoint in scoring in the last thirty years occurred in the 1998 \
        season, where the league averaged 91.6 points per game. This era of \
        basketball was characterized by slow, physical isolation basektball \
        from the likes of players like Michael Jordan, Kobe Bryant, and Tim Duncan.",
      )
      .style("font-size", "14px");

    const foreignObject2 = svg3
      .append("foreignObject")
      .attr("x", 570)
      .attr("y", -37)
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
        "https://ew.com/thmb/XoWCPkHqWW_fgYYutiw9ugmthbw=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc()/kobe-bryant-0d4dffb9eac446ec8aa5a5536f6ca1ff.jpg",
      )
      // .attr("width", 500); // Adjust the width as needed
      .attr("height", 200);
  }

  function renderBarChart5(year) {
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

    svg5 = d3
      .select("#chart5")
      .append("svg")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
      .append("g")
      .attr("transform", `translate(${margin.left},${margin.top})`);

    x = d3.scaleBand().domain(keys).range([0, width]).padding(0.1);

    y = d3.scaleLinear().domain([minVal - minVal, maxVal - minVal]).range([height, 0]);

    svg5
      .append("g")
      .attr("transform", `translate(0,${height})`)
      .call(d3.axisBottom(x))
      .selectAll("text")
      .attr("transform", "rotate(-45)")
      .style("text-anchor", "end")
      .style("font-size", "12px");

    svg5.append("g").call(d3.axisLeft(y));

    svg5
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

    svg5
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

        const tooltip = svg5
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
        updateTooltipPosition(event, d, svg5);
      })
      .on("mousemove", (event, d) => {
        updateTooltipPosition(event, d, svg5);
      })
      .on("mouseout", (event) => {
        svg5.select(".tooltip").remove();
      });

    svg5
      .append("text")
      .attr("transform", `translate(${width / 2}, ${height + 120})`) // Adjust the position as needed
      .style("text-anchor", "middle")
      .text("Teams")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");

    svg5
      .append("text")
      .attr("transform", "rotate(-90)")
      .attr("y", 0 - margin.left) // Adjust the position as needed
      .attr("x", 0 - height / 2)
      .attr("dy", "1em")
      .style("text-anchor", "middle")
      .text("Average Points Per Game Difference")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");

    const lineData1 = [
      { x: 0, y: 104 },
      { x: 1220, y: 104 },
    ];

    const lineFunction = d3
      .line()
      .x((d) => d.x)
      .y((d) => d.y);

    svg5
      .append("path")
      .attr("d", lineFunction(lineData1))
      .attr("stroke", "red")
      .attr("stroke-width", 2)
      .attr("fill", "none");

    const lineData2 = [
      { x: 805, y: 104 },
      { x: 860, y: 40 },
    ];

    svg5
      .append("path")
      .attr("d", lineFunction(lineData2))
      .attr("stroke", "gray")
      .attr("stroke-width", 2)
      .attr("fill", "none");

    const lineData3 = [
      { x: 860, y: 40 },
      { x: 1260, y: 40 },
    ];

    svg5
      .append("path")
      .attr("d", lineFunction(lineData3))
      .attr("stroke", "gray")
      .attr("stroke-width", 2)
      .attr("fill", "none");

    svg5
      .append("text")
      .attr("x", 900)
      .attr("y", 158)
      .attr("font-size", "14px")
      .attr("fill", "black")
      .text("")
      .style("font-size", "20px");

    const foreignObject = svg5
      .append("foreignObject")
      .attr("x", 860)
      .attr("y", -27)
      .attr("width", 400)
      .attr("height", 100);

    const div = foreignObject
      .append("xhtml:div")
      .style("font-size", "14px")
      .style("color", "black")
      .style("text-align", "justify");

    div
      .html(
        "In the most recently completed NBA season, teams averaged 114.7 points \
        per game, with the game nowadays revolving around the three point shot \
        and efficient play in general. This playstyle is pioneered by players \
        like Stephen Curry, LeBron James, and Kevin Durant.",
      )
      .style("font-size", "14px");
  }
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

  function teamData(y, d, teams) {
    return { year: y, pts: d, team: teams };
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

  function updateTeam3Pts() {
    team_3pts = {};
    let year_data_3 = cloneDeep(threepointData);
    for (let tm of team_3pt_line) {
      counter = 0;
      team_3pts[tm] = [];
      for (let yer of year_data_3) {
        if (parseFloat(yer[tm]) > 0) {
          var toadd = { year: parseInt(yer.Year), pts: parseFloat(yer[tm]) };
          team_3pts[tm][counter] = toadd;
          counter++;
        }
      }
    }
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
    } else {
      renderYearAvg();
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

  function update3ptLine(dataArray) {
    if (team_3pt_line.length != 0) {
      u3 = line3.selectAll(".liness2").data(Object.values(team_3pts));

      u3.enter()
        .append("path")
        .attr("class", "liness2")
        .merge(u3)
        .transition()
        .duration(1000)
        .attr(
          "d",
          d3
            .line()
            .x(function (d) {
              return xLine3(d.year);
            })
            .y(function (d) {
              return yLine3(d.pts);
            })
            .curve(d3.curveBasis),
        )
        .attr("fill", "none")
        .attr("stroke", (d, i) => colors[i])
        .attr("stroke-width", 2.5);
      u3.exit().remove();
    } else {
      renderYearAvg3pt();
    }

    function renderYearAvg3pt() {
      u3 = line3.selectAll(".line2").data([year_three_avg]);
      u3.enter()
        .append("path")
        .attr("class", "line2")
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
      u3.exit().remove();
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

  function checkbox3pt(data) {
    select2 = d3
      .select("#body3pt")
      .append("select")
      .attr("multiple", true)
      .attr("size", 8);
    select2
      .selectAll("option")
      .data(data)
      .enter()
      .append("option")
      .attr("value", (d) => d)
      .text((d) => d);

    select2.on("change", function () {
      let options2 = this.selectedOptions;
      team_3pt_line = Array.from(options2).map((option2) => option2.value);
      updateTeam3Pts();
      update3ptLine(team_3pts);
    });
  }

  function renderLinePlotThree(dataArray) {
    const margin = { top: 40, right: 120, bottom: 150, left: 60 };
    const width = 1400 - margin.left - margin.right;
    height = 600 - margin.top - margin.bottom;

    let minimumX = Math.min(...year_avg.map((obj) => parseInt(obj["year"])));
    let maximumX = Math.max(...year_avg.map((obj) => parseInt(obj["year"])));
    let minimumY = 0;
    let maximumY = Math.max(...year_avg.map((obj) => obj["avg"])) + 20;

    xLine3 = d3.scaleLinear().domain([1979, maximumX]).range([0, width]);

    yLine3 = d3.scaleLinear().domain([minimumY, 40]).range([height, 0]);

    line3 = d3
      .select("#linechart2")
      .append("svg")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
      .append("g")
      .attr("transform", `translate(${margin.left},${margin.top})`);
    line3.exit().remove();

    line3
      .append("g")
      .attr("transform", `translate(0,${height})`)
      .call(d3.axisBottom(xLine3));

    line3.append("g").call(d3.axisLeft(yLine3));

    line3
      .selectAll("line2")
      .data([year_three_avg])
      .enter()
      .append("path")
      .attr(
        "d",
        d3
          .line()
          .x(function (d) {
            return xLine3(d.year);
          })
          .y(function (d) {
            return yLine3(d.avg);
          })
          .curve(d3.curveBasis),
      )
      .attr("class", "liness2")
      .attr("fill", "none")
      .attr("stroke", "red")
      .attr("stroke-width", 2.5);

    line3
      .append("text")
      .attr("transform", "rotate(-90)")
      .attr("y", 0 - margin.left) // Adjust the position as needed
      .attr("x", 0 - height / 2)
      .attr("dy", "1em")
      .style("text-anchor", "middle")
      .text("Average Three Point Attempts Per Game")
      .style(
        "font-family",
        "Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif",
      )
      .style("font-size", "20px");

    line3
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

  // function checkbox(data) {
  //   select = d3
  //     .select("#body")
  //     .append("select")
  //     .attr("multiple", true)
  //     .attr("size", 8);
  //   select
  //     .selectAll("option")
  //     .data(data)
  //     .enter()
  //     .append("option")
  //     .attr("value", (d) => d)
  //     .text((d) => d);

  //   select.on("change", function () {
  //     const options = this.selectedOptions;
  //     team_line = Array.from(options).map((option) => option.value);
  //     updateTeamPts();
  //     updateLine(team_pts);
  //   });
  // }


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

<main>
  <div id="title">
    <h1>
      Is Defense Dying in the NBA?<img 
        src="https://images.ctfassets.net/h8q6lxmb5akt/5qXnOINbPrHKXWa42m6NOa/421ab176b501f5bdae71290a8002545c/nba-logo_2x.png"
        ,
        alt="NBA" class="NBA_img"
      />
    </h1>
    
    <div id="hook">
      <div class="container">
        <div class="text_test">Common discourse around NBA fans and media is 
          that scoring is getting out of hand. This season, it seems like 
          someone is dropping 50 points in a game every week. Pioneered by 
          Steph Curry and the Golden State Warriors, teams are shooting more 
          threes than ever before. </div>
        <img id="first_hook" src="https://a.espncdn.com/photo/2016/0303/r60200_2_1296x729_16-9.jpg" alt="pookie">
      </div>    
      <div class="container">
        <img id="second_hook" src="https://cdn.nba.com/manage/2021/08/michael-jordan-looks.jpg" alt="goat">
        <div class="text_test">There is a narrative circulating that this spike 
          in scoring is ruining the league. Many fans want to go back to the 
          “good old days” of  “real” basketball, when players like Michael 
          Jordan and Kobe Bryant maintained the integrity of the league.</div>
      </div>
      <div style="text-align:center">
        <div style="font-size:20px">Our following visual article aims to explore 
          historical scoring trends in the NBA and assess whether this panic is warranted.</div>
      </div>    
    </div>
    <div>
    </div>
  </div>
  <div id="chart2" class="chart_class">
    <h2 style="text-align: left;">
      NBA Teams Difference in Average Points per Game in 1953 From All Time
      Lowest Average
    </h2>
  </div>
  <div class="paragraph_annotation">
    <p>Let’s start our analysis of the history of scoring in the NBA in 1953. 
      This season, the Milwaukee Hawks (now Atlanta Hawks) averaged a measly 70 
      points per game. There were only 8 teams in the league and basketball 
      during this era barely resembles modern basketball. For the rest of our 
      analysis, we will be comparing all other teams and years relative to this 
      lowpoint in scoring, which will be reflected in the axes values.</p>
  </div>
  <div id="chart3" class="chart_class">
    <h2 style="text-align: left;">
      NBA Teams Difference in Average Points per Game in 1980 From All Time
      Lowest Average
    </h2>
  </div>
  <div class="paragraph_annotation">
    <p>We now move forward to 1980. The NBA is thriving in this era, with the 
      Showtime Lakers and the Magic versus Bird rivalry captivating fans. This 
      year, the league had a scoring average of 108.1 points per game, which 
      was pretty typical of the 70s and 80s. Trail blazed by the Showtime Lakers, 
      teams were playing at an unprecedented pace. The three-point line was also 
      introduced in 1979, allowing another avenue of elevated scoring although 
      teams were not quick to prioritize it.</p>
  </div>
  <div id="chart4" class="chart_class">
    <h2 style="text-align: left;">
      NBA Teams Difference in Average Points per Game in 1998 From All Time
      Lowest Average
    </h2>
  </div>
  <div class="paragraph_annotation">
    <p>We are now in 1998, and the turn of the century is characterized by much 
      slower and physical play. During the 1998 season, the league averaged 91.6 
      points per game, the lowest in the last thirty years and since the 1950s, 
      when professional basketball was still in its infancy. Teams leaned heavily 
      into isolation plays for their star players like Michael Jordan and Kobe 
      Bryant, as the more physical defense aided by lenient foul calling made 
      offense hard to come by. This style of play would persist well into the 2000s.</p>
  </div>
  <div id="chart5" class="chart_class">
    <h2 style="text-align: left;">
      NBA Teams Difference in Average Points per Game in 2022 From All Time
      Lowest Average
    </h2>
  </div>
  <div class="paragraph_annotation">
    <p>The modern game is dominated by three point shooting and playbooks 
      corroborated by advanced analytics. The skill level has reached an all-time 
      high in the league as it has become more international than ever before, 
      with the last five MVP awards going to foreign born players. In the most 
      recent season, the league average was 114.7 points per game, a value not 
      seen since the 1960s. Offensive superstars like Steph Curry, Lebron James, 
      Nikola Jokic, and Kevin Durant dominate the headlines and are the standard 
      of emulation for all teams around the league.</p>
      <p>Below is an interactive, playable version of the static bar graphs above.</p>
  </div>
  <div id="chart" class="chart_class">
    <h2 style="text-align: left;">
      NBA Teams Difference in Average Points per Game in {year} From All Time Lowest
      Average
    </h2>
    <div id="overlay">
      <!-- svelte-ignore a11y-label-has-associated-control -->
      <label>{year}</label>
      <input
        type="range"
        min="1950"
        max="2022"
        value={year}
        on:input={(e) => updateYear(+e.target.value)}
      />
      {#if playing}
        <button on:click={stop}>Stop</button>
      {:else}
        <button on:click={play}>Play</button>
      {/if}
    </div>
  </div>
  <div class="paragraph_annotation">
    <p>While there may be some reason for worry, the 
      widespread panic and announcements of the league’s downfall are greatly 
      overblown. While scoring has increased drastically since the early 2000s, 
      the current levels of unscoring are not completely unprecedented. Many 
      years throughout the 70s and 80s, the league average would be north of 
      110 points per game and therefore in the same ballpark as the current 
      statistics. We believe that there is so much panic due to the demographic 
      of NBA fans. Majority of NBA fans nowadays grew up either in the 90s or 
      2000s with a childhood highlighted by either Jordan’s or Bryant’s dominance. 
      These fans are conditioned to the slower, more physical playstyle and have 
      only witnessed the NBA become “softer” and scoring increase in their 
      lifetimes. However, if you consider the entirety of modern basketball, 
      the 90s and 2000s are a valley in scoring and it is very possible that 
      scoring follows a oscillating, sinusodial pattern and we are currently 
      just at a peak. The one reservation we will make is that the rate scoring 
      has increased in the last decade is unprecedented. In less than the last 
      decade, the league average has increased from 100 to over 114 points per 
      game. While the current average pace has not exceeded that of the run-and-gun 
      Laker’s era in the 80s, we could be getting there soon and coupled with 
      the proliferation of the three point shot, a truly unprecedented level of 
      scoring could be on the horizon.</p>
      <p>Below is an interactive line chart that shows scoring average over the years.</p>
  </div>
  <div id="highlightable-box" class="highlightable-box">
    <!-- Highlightable elements (team names) will be rendered here -->
  </div>
  <div id="body"><h2>Select teams to view:</h2><p>You can select multiple teams by either click and drag or by holding command/ctrl and clicking on the desired teams</p></div>
  <div id="linechart" class="chart_class"><h2>Average Points Per Game over the Years</h2></div>
  <div class="highlightable-box"><h2>Conclusion</h2></div>
  <div class="paragraph_annotation">
    <p>NBA teams are scoring more and more points recently AND many fans are 
      worried. BUT these fans have most likely only been watching since 90s and 
      2000s and have therefore only witnessed an increase in scoring in their 
      lifetimes. THEREFORE the current levels of scoring and the state of the 
      game are not necessarily things to be worried about as we have seen 
      similar levels of scoring in the past.</p>
  </div>
  <div class="highlightable-box"><h2>Possible Explanation for Recent Scoring Spike</h2></div>
  <div class="paragraph_annotation">
    <p>There has been a recent explosion in the usage of the three point shot, 
      with the average number of three point attempts going from 18 in 2011 to 
      nearly doubling in the 2021 with 35.2. With more three pointers being 
      attempted and teams greatly valuing high percentage three point shooters, 
      expected scoring totals greatly increase. This is coupled with a rebound 
      in the pace of play from the molasses marred speed of the 90s and 2000s 
      to the more uptempo style of the 70s and 80s. For context, the slowest 
      paced team this past season was still faster than the fastest team in 2001. 
      The combination of these two phenomena is a possible cause for recent uptick.</p>
      <p>Below are two graphs that help visualize the phenomena.</p>
  </div>
  <div id="body3pt"><h2>Select teams to view:</h2><p>You can select multiple teams by either click and drag or by holding command/ctrl and clicking on the desired teams</p></div>
  <div id="linechart2" class="chart_class"><h2>Average Three Point Attempts Per Game Over the Years</h2></div>
  <div id="linechartpace" class="chart_class"><h2>NBA Average Pace Over the Years</h2></div>
  <!-- <div id="text">
    <h3 style="text-align: left;">Design Process and Decisions</h3>
    <p style="font-size: 24;">
      A common narrative and topic of debate amongst NBA (National Basketball
      Association) fans and media is whether teams are scoring too many points
      and defense is becoming a smaller part of the game. We decided to explore
      this narrative by plotting the average points scored per game per team
      over the history of the league, starting from 1950 when the league only
      comprised of 8 teams.
    </p>
    <p>
      For our data, we decided to get it from Basketball Reference, a widely
      trusted, although not official, source for basketball data amongst NBA
      fans and media. We chose to scrape data from Basketball Reference as
      opposed to NBA.com, the league’s official source for data, because
      Basketball Reference was much easier to get data and the data was cleaner.
      Cleaning the data involved filling in zeros for the years when certain
      teams did not exist yet. Otherwise, the Basketball Reference data was
      clean and easy to work with.
    </p>
    <p>
      Regarding design decisions, we knew we wanted to do a slider to show data
      for different years and be able to play it to show the progression of
      scoring over the years. As a result, we chose to use a bar chart as it was
      an efficient way to display data for all 30 teams (which are essentially
      30 categories) for a single year. On top of each individual bar, we
      decided to add the team’s logo. This is a quick and easy way to identify a
      team’s bar, if one knows the team logos, without having to look all the
      way down to the x-axis on top of helping differentiate bars when many can
      have similar values. For each bar, we have a tooltip that shows the exact
      points per game (PPG) values for the team being hovered over. This is
      helpful as in a bar chart it can be hard to get an exact value, especially
      in the wider chart that we have. The tooltip updates to whatever team it
      hovers over and the value updates with the incrementing year as long as
      one’s mouse is moving. For the font of the graph and axes titles, we
      decided to use the impact font as it matches the font commonly used in NBA
      graphics on social media and titles of NBA articles. The color of the bars
      matches the color of a basketball to give a bit of additional semantic
      encoding.
    </p>
    <p>
      Playing the slider and observing the changes over the years, we can see
      that teams were scoring very few points in the early years of the league,
      likely as basketball was still being fleshed out as a serious sport. Over
      the next three decades until around the 1990s, teams would score a
      respectable amount of points, with many teams hovering around 110 points
      per game. Starting in the 90s, teams started scoring significantly less
      points and averages reached lows not seen since the inception of the
      league in the 2000s, when some teams were averaging less than 90 points
      per game. This trend of low scoring games stopped around the mid 2010s,
      with team scoring averages increasing at an unprecedented rate in the last
      few years. Our conclusion is that while there is reason for concern with
      the quick uptick in scoring in not even the last decade. However, this
      level of scoring has been seen before in the 70s and 80s. Therefore, this
      fear is most likely exaggerated by older NBA fans who grew up watching the
      lower scoring style of basketball of the 1990s and 2000s.
    </p>
    <p>
      To begin our development process, we had to decide on a dataset. We all
      agreed that the datasets we had used for Project 2 were limiting, so we
      decided to find some new, bigger data. Nathan and Walter were confident in
      their domain knowledge of the NBA and basketball. Additionally, they knew
      that there is an abundance of data available from sources such as
      Basketball Reference and NBA.com.
    </p>
    <p>
      Approaching the project from the perspective of making an interactive
      visualization, we decided from the start to do something that changes with
      time, incorporating a slider like we did in Lab 6 and one of the D3
      examples in lecture. In using a slider, we decided to not do a line chart
      as it would not make much sense, because line charts typically show
      progression and trends over a period of time. Having a line for each NBA
      team would have been too messy. With the bar chart, we decided to limit
      the y-axis to a range from 0 to 60. The value represented by the bar
      charts represents the difference in points per game for that team that
      year from the all time lowest average (70.0 by the Atlanta Hawks in 1953).
      This allows us to see how much teams have improved at scoring since the
      worst team near the beginning of the league. This transformation of the
      data is key as it makes small differences more evident. A seemingly small
      difference of 5 PPG is extremely significant in the context of basketball.
    </p>
    <p>
      With the slider, Ahmed made it so that it can be played automatically and
      that it would increment at a rate of about a year per second. This rate
      allows for the transitions to fully show and therefore the bar will show
      the corect value before changing to the next year. Regarding the tooltip,
      we decided to add it as we believed just having the slider has not enough
      interactivity. The tooltip pops up when you hover over the bar for a team
      and shows the team name being hovered over as wel as the exact PPG value,
      as this can be hard to get on a bar chart. During the development process,
      Walter initially had the tooltip follow the position of the mouse.
      However, he later decided to attach the tooltip to the associated bar, as
      there was a problem with the position of the tooltip on different
      computers. Depending on one’s browser and the size of one’s screen, the
      tooltip would sometimes not be close to the mouse, instead being way off
      while still moving with the mouse in some manner. Nathan decided that
      adding logos would make the bars easier to follow and add helpful
      redundant encodings. As mentioned in the design decisions above, this
      allows for easier differentiation and identification of the bars without
      having to trace the bar all the way down to the x-axis. Nathan also
      decided to use the links of the team logos from the official NBA website
      so that when a team’s logo gets updated, this change would be reflected in
      our visualization as well.
    </p>
    <p>
      We were initially planning on making each bar match the colors of the
      associated team as well, however Nathan and Ahmed decided that this would
      be too confusing and not as color blind friendly. As a result, Walter
      decided on the orange color to match that of a basketball and the black
      stroke to create contrast between the bars themselves as well as the
      background and axes. Walter also decided on the title font based on what
      he had seen before in NBA graphics and articles. As a finishing touch, he
      also added the NBA logo in the top-left corner of the page to quickly let
      readers know that they were dealing with a basketball visualization if
      they had some prior knowledge about the professional league.
    </p>
    <p>
      Roles were more or less mentioned above in the development process. Ahmed
      was responsible for setting up the Svelte framework and made the slider as
      well the function to update the bar chart to match the year. Nathan was
      responsible for creating the logos and having them match the height of the
      bars to create a helpful visual encoding. Walter was responsible for
      creating the tooltip and the remaining auxiliary design decisions. Ahmed
      was also responsible for setting up the website. In general, we worked
      together for the majority of the project and constantly bounced ideas and
      feedback off of each other.
    </p>
  </div> -->
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

  .NBA_img {
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

  .highlightable-box, #body, #body3pt {
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
    font-size:18px;
    text-align: justify;

  }

  #first_hook, #second_hook {
    height: 250px;
  }

  #linechart, #linechart2 {
    margin-top: -100px;
    margin-bottom: -50px;
  }

</style>
