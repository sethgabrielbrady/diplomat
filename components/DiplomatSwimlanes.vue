<script>
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
  </div>
</template>

<style scoped>

  .swimlane {
    color:#FFF22F;
    font-family: 'NOIRetBLANC', serif;
    font-size: 2em;
  }

</style>