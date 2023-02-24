<template>
  <!-- BG Houses (between the building and front houses) -->
  <div class="container">
    <img alt="houses" class="bg" :class="{ scoreon: scorecard}" src="../../assets/images/bg/houses.svg">
  </div>
</template>

<script>
export default {
  name: 'BgHouses',
  props: {
    scorecard: Boolean
  }
}
</script>

<style scoped>
.bg {
  width: 100%;
  position: fixed;
  transition-duration: 600ms;
  bottom: -25%;
  left: 0;
  opacity: 1;
}
.scoreon {
  bottom: 240px;
  opacity: 0.4;
}

@media only screen and (max-height: 700px) {
    .bg {
      opacity: 0;
    }
  }
  @media only screen and (max-width: 900px) {
    .bg {
      bottom: -30px;
    }
  }
</style>
