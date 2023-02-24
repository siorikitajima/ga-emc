<template>
  <!-- The rear building image -->
  <div class="container">
    <img alt="buildings" class="bg" :class="{ scoreon: scorecard}" src="../../assets/images/bg/buildings.svg">
  </div>
</template>

<script>
export default {
  name: 'BgBuildings',
  props: {
    scorecard: Boolean
  }
}
</script>

<style scoped>
.bg {
  width: 100%;
  position: fixed;
  bottom: -100px;
  left: 0;
  transition-duration: 900ms;
  opacity: 0.7;
}
.scoreon {
  bottom: 60px;
  opacity: 0.5;
}

@media only screen and (max-height: 700px) {
    .bg {
      opacity: 0;
    }
  }
  @media only screen and (max-width: 900px) {
    .bg {
      bottom: 0;
    }
  }
</style>
