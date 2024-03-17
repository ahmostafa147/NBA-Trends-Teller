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
  import Pic from '../components/pic.svelte'
  let count, index, offset, progress;
  $: console.log(index)
  let isVisible1 = false;
  let isVisible9 = false;

  $: if (index === 0) {
    isVisible9 = true;
  } else {
    isVisible9 = false;
  }
  
  $: if (index === 1) {
    isVisible1 = true;
  } else {
    isVisible1 = false;
  }
  let isVisible2 = false;

  $: if (index === 2) {
    isVisible2 = true;
  } else {
    isVisible2 = false;
  }
  let isVisible3 = false;

  $: if (index === 3) {
    isVisible3 = true;
  } else {
    isVisible3 = false;
  }
  let isVisible4 = false;

  $: if (index === 4) {
    isVisible4 = true;
  } else {
    isVisible4 = false;
  }
  let isVisible5 = false;

  $: if (index === 5) {
    isVisible5 = true;
  } else {
    isVisible5 = false;
  }

  let isVisible6 = false;

  $: if (index === 7) {
    isVisible6 = true;
  } else {
    isVisible6 = false;
  }

  let isVisible7 = false;

  $: if (index >= 8) {
    isVisible7 = true;
  } else {
    isVisible7 = false;
  }

  let isVisible8 = false;

  $: if (index >= 9) {
    isVisible8 = true;
  } else {
    isVisible8 = false;
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
  </div>
  <div class="foreground" slot="foreground">
    <!-- <App {index}/> -->
    <section style="height:85vh">
      <div id="please9" class:visible={isVisible9}><Pic /></div>
    </section>
    <section >
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
    <section style="height:15vh"></section>
    <section style="height:155vh">
      <div id="please6" class:visible={isVisible6}><Ptsline /></div>
    </section>
    <section>
      <div id="please7" class:visible={isVisible7}><Pts3line /></div>
    </section>
    <section>
      <div id="please8" class:visible={isVisible8}><Pace /></div>
    </section>
  </div>
</Scroller>

<style>
  #please7 {
    width: 100%;
    height: 100vh; /* check problem when setting width */
    position: absolute;
    opacity: 0;
    visibility: hidden;
    transition:
      opacity 2s,
      visibility 2s;
  }

  #please7.visible {
    opacity: 1;
    visibility: visible;
  }
  #please8 {
    width: 100%;
    height: 100vh; /* check problem when setting width */
    position: absolute;
    opacity: 0;
    visibility: hidden;
    transition:
      opacity 2s,
      visibility 2s;
  }

  #please8.visible {
    opacity: 1;
    visibility: visible;
  }
    #please2 {
    width: 100%;
    height: 100vh; /* check problem when setting width */
    opacity: 0;
    visibility: hidden;
    transition:
      opacity 3s,
      visibility 3s;
  }

  #please2.visible {
    opacity: 5;
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
    height: 120vh;
    color: white;
    text-align: center;
    max-width: 7500px; /* adjust at will */
    color: black;
    padding: 1em;
    margin: 0 0 2em 0;
  }
</style>
