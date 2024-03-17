<script></script>
<main>
  <div id="title">
    <h1>
      Is Defense Dying in the NBA?<img
        src="https://images.ctfassets.net/h8q6lxmb5akt/5qXnOINbPrHKXWa42m6NOa/421ab176b501f5bdae71290a8002545c/nba-logo_2x.png"
        ,
        alt="NBA"
        class="NBA_img"
      />
    </h1>

    <div id="hook">
      <div class="container">
        <div class="text_test">
          Common discourse around NBA fans and media is that scoring is getting
          out of hand. This season, it seems like someone is dropping 50 points
          in a game every week. Pioneered by Steph Curry and the Golden State
          Warriors, teams are shooting more threes than ever before.
        </div>
        <img
          id="first_hook"
          src="https://a.espncdn.com/photo/2016/0303/r60200_2_1296x729_16-9.jpg"
          alt="pookie"
        />
      </div>
      <div class="container">
        <img
          id="second_hook"
          src="https://cdn.nba.com/manage/2021/08/michael-jordan-looks.jpg"
          alt="goat"
        />
        <div class="text_test">
          There is a narrative circulating that this spike in scoring is ruining
          the league. Many fans want to go back to the “good old days” of “real”
          basketball, when players like Michael Jordan and Kobe Bryant
          maintained the integrity of the league.
        </div>
      </div>
      <div style="text-align:center">
        <div style="font-size:20px">
          Our following visual article aims to explore historical scoring trends
          in the NBA and assess whether this panic is warranted.
        </div>
      </div>
    </div>
    <div></div>
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
    top: -30px;
    left: -500px;
    height: 30vh;
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
