<script>
import { Octokit, App } from "octokit";

  export default defineComponent({
    setup() {
      return {
        swimLanes: ['Considering', 'On Deck', 'In Progress', 'In Review', 'Done']
      }
    },
  })
</script>

<template>
  <div class="swimlanes-container">
    <div class="swimlane">
      <p>{{ swimLanes[1] }}</p>
    </div>
    <div class="svg-arrow">
      <svg width="3em" height="3em" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
      <path d="M4 12H20M20 12L16 8M20 12L16 16" stroke="#FFF22F" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
      </svg>
    </div>
  </div>
</template>

<style scoped>

  .swimlane {
    color:#FFF22F;
    font-family: 'NOIRetBLANC', serif;
    font-size: 2em;
  }

  .svg {
    width: max-content;
  }
  .svg-arrow {
    cursor: pointer;
    position: absolute;
    right: 3em;
    transform: translateY(-3em);
  }

</style>