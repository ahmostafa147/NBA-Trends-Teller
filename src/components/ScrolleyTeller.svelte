<script>
  import Scroller from "@sveltejs/svelte-scroller";
  import App from "./App.svelte";
  import Bar1 from "../components/Bar1.svelte";
  import Bar2 from "../components/Bar2.svelte";
  import Bar3 from "../components/Bar3.svelte";
  import Bar4 from "../components/Bar4.svelte";
  import Bar5 from "../components/Bar5.svelte";
  import Ptsline from "../components/ptsline.svelte";
  import Pts3line from "../components/3ptsline.svelte";
  import Pace from "../components/pace.svelte";
  let count, index, offset, progress;
  $: console.log(index)
  let isVisible1 = false;

  $: if (index === 0) {
    isVisible1 = true;
  } else {
    isVisible1 = false;
  }
  let isVisible2 = false;

  $: if (index === 1) {
    isVisible2 = true;
  } else {
    isVisible2 = false;
  }
  let isVisible3 = false;

  $: if (index === 2) {
    isVisible3 = true;
  } else {
    isVisible3 = false;
  }
  let isVisible4 = false;

  $: if (index === 3) {
    isVisible4 = true;
  } else {
    isVisible4 = false;
  }
  let isVisible5 = false;

  $: if (index === 4) {
    isVisible5 = true;
  } else {
    isVisible5 = false;
  }

  let isVisible6 = false;

  $: if (index === 5) {
    isVisible6 = true;
  } else {
    isVisible6 = false;
  }
</script>

<Scroller
  top={0.0}
  bottom={1}
  threshold={0.5}
  bind:count
  bind:index
  bind:offset
  bind:progress
>
  <div class="background" slot="background" id="background">
    <div class="progress-bars">
      <p>current section: <strong>{index + 1}/{count}</strong></p>
      <progress value={count ? (index + 1) / count : 0} />

      <p>offset in current section</p>
      <progress value={offset || 0} />

      <p>total progress</p>
      <progress value={progress || 0} />
    </div>
  </div>
  <div class="foreground" slot="foreground">
    <!-- <App {index}/> -->
    <section>
      <div id="please2" class:visible={isVisible1}><Bar2 /></div>
    </section>
    <section>
      <div id="please3" class:visible={isVisible2}><Bar3 /></div>
    </section>
    <section>
      <div id="please4" class:visible={isVisible3}><Bar4 /></div>
    </section>
    <section>
      <div id="please5" class:visible={isVisible4}><Bar5 /></div>
    </section>
    <section>
      <div id="please1" class:visible={isVisible5}><Bar1 /></div>
    </section>
    <section>
      <div id="please6" class:visible={isVisible6}><Ptsline /></div>
    </section>
  </div>
</Scroller>

<style>
    #please2 {
    width: 100%;
    height: 100vh; /* check problem when setting width */
    position: absolute;
    opacity: 0;
    visibility: hidden;
    transition:
      opacity 2s,
      visibility 2s;
  }

  #please2.visible {
    opacity: 1;
    visibility: visible;
  }

  #please3 {
    width: 100%;
    height: 100vh; /* check problem when setting width */
    position: absolute;
    opacity: 0;
    visibility: hidden;
    transition:
      opacity 2s,
      visibility 2s;
  }

  #please3.visible {
    opacity: 1;
    visibility: visible;
  }

  #please4 {
    width: 100%;
    height: 100vh; /* check problem when setting width */
    position: absolute;
    opacity: 0;
    visibility: hidden;
    transition:
      opacity 2s,
      visibility 2s;
  }

  #please4.visible {
    opacity: 1;
    visibility: visible;
  }

  #please5 {
    width: 100%;
    height: 100vh; /* check problem when setting width */
    position: absolute;
    opacity: 0;
    visibility: hidden;
    transition:
      opacity 2s,
      visibility 2s;
  }

  #please5.visible {
    opacity: 1;
    visibility: visible;
  }

  #please6 {
    width: 100%;
    height: 100vh; /* check problem when setting width */
    position: absolute;
    opacity: 0;
    visibility: hidden;
    transition:
      opacity 2s,
      visibility 2s;
  }

  #please6.visible {
    opacity: 1;
    visibility: visible;
  }

  #please1 {
    width: 100%;
    height: 100vh; /* check problem when setting width */
    position: absolute;
    opacity: 0;
    visibility: hidden;
    transition:
      opacity 2s,
      visibility 2s;
  }

  #please1.visible {
    opacity: 1;
    visibility: visible;
  }

  .background {
    width: 100%;
    height: 100vh;
    position: relative;
    outline: green solid 3px;
  }

  .foreground {
    width: 50%;
    margin: auto;
    height: auto;
    position: relative;
  }

  .progress-bars {
    position: absolute;
    background: rgba(170, 51, 120, 0.2) /*  40% opaque */;
    visibility: visible;
  }

  section {
    height: 80vh;

    color: white;
    text-align: center;
    max-width: 750px; /* adjust at will */
    color: black;
    padding: 1em;
    margin: 0 0 2em 0;
  }
</style>
