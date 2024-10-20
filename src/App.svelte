<script>
  import { onMount } from "svelte";
  import mermaid from "mermaid";
  import panzoom from "panzoom";
  import flowchart from "./lib/flowchart.mmd?raw";

  let container;
  let diagram;
  let zoom = 1;
  let pz;

  onMount(async () => {
    // Initialize mermaid with custom config
    mermaid.initialize({
      startOnLoad: false,
      theme: "default",
      securityLevel: "loose",
    });

    try {
      // Render the mermaid diagram
      const { svg } = await mermaid.render("flowchart", flowchart);
      diagram.innerHTML = svg;

      // Initialize panzoom
      pz = panzoom(diagram.querySelector("svg"), {
        maxZoom: 4,
        minZoom: 0.1,
        bounds: true,
        boundsPadding: 0.1,
      });

      // Update zoom display when zooming
      pz.on("zoom", (e) => {
        zoom = e.getTransform().scale.toFixed(2);
      });
    } catch (error) {
      console.error("Error rendering diagram:", error);
    }
  });

  function resetView() {
    if (pz) {
      pz.moveTo(0, 0);
      pz.zoomAbs(0, 0, 1);
      zoom = 1;
    }
  }

  function zoomIn() {
    if (pz) pz.smoothZoom(0, 0, 1.5);
  }

  function zoomOut() {
    if (pz) pz.smoothZoom(0, 0, 0.667);
  }
</script>

<div class="container">
  <h1>Multilingual Students and Students with Learning Differences</h1>

  <p>
    It can be challenging for educators in a school environment to address the
    needs of students with different levels of English proficiency. For
    struggling students it is neccessary to disentagle language problems from
    learning disabilities as well. See the flowchart below for a process to
    address struggling students. Click the links to proceed to articles and
    resources related to some of the steps.
  </p>
  <p>
    Made with <a href="https://mermaid-js.github.io/mermaid/">Mermaid</a> by James
    Smith in 2024.
  </p>
  <div class="controls">
    <button on:click={zoomIn}>Zoom In</button>
    <button on:click={zoomOut}>Zoom Out</button>
    <button on:click={resetView}>Reset View</button>
    <span>Zoom: {zoom}x</span>
  </div>
  <div class="diagram-container" bind:this={container}>
    <div class="diagram" bind:this={diagram}></div>
  </div>
</div>

<style>
  .container {
    width: 100%;
    max-width: 1200px;
    margin: 1rem auto;
    height: 100vh;
    display: flex;
    flex-direction: column;
    padding: 1rem;
  }

  .controls {
    display: flex;
    gap: 1rem;
    padding: 1rem;
    background: #f5f5f5;
    border-radius: 4px;
    align-items: center;
  }

  .controls button {
    padding: 0.5rem 1rem;
    background: #4a4a4a;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }

  .controls button:hover {
    background: #363636;
  }

  .diagram-container {
    flex: 1;
    overflow: hidden;
    border: 1px solid #ddd;
    border-radius: 4px;
    margin-top: 1rem;
  }

  .diagram {
    width: 100%;
    height: 100%;
  }

  .diagram :global(svg) {
    width: 100%;
    height: 100%;
  }
</style>
