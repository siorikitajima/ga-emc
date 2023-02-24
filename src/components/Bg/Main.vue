<template>
  <div class="container">
    <!-- The bottom black banner -->
    <div class="blkbanner" :class="{ scron: scorecard}"></div>

    <div class="onehouse">
      <img class="window01" :class="{ scron: scorecard}" :src="imgPath01" alt="window01">
      <img class="window02" :class="{ scron: scorecard}" :src="imgPath02" alt="window02">
      <img alt="buildings" class="house" id="house1" :class="{ scoreon: scorecard}" src="../../assets/images/bg/house-01.svg">
    </div>

    <div class="onehouse three">
      <img class="window05" :class="{ scron: scorecard}" :src="imgPath05" alt="window05">
      <img class="window06" :class="{ scron: scorecard}" :src="imgPath06" alt="window06">
      <img alt="buildings" class="house" id="house4" :class="{ scoreon: scorecard}" src="../../assets/images/bg/house-02.svg">
    </div>

    <div class="onehouse four">
      <img class="window03" :class="{ scron: scorecard}" :src="imgPath03" alt="window03">
      <img alt="buildings" class="house" id="house2" :class="{ scoreon: scorecard}" src="../../assets/images/bg/house-03.svg">
    </div>

    <div class="onehouse">
      <img class="window04" :class="{ scron: scorecard}" :src="imgPath04" alt="window04">
      <img alt="buildings" class="house" id="house3" :class="{ scoreon: scorecard}" src="../../assets/images/bg/house-04.svg">
    </div>

  </div>
</template>

<script>
export default {
  name: 'BgMain',
  data() {
    return {
      grade: "A",
      image01: {
        0: require('@/assets/images/animation/default/animation-01.svg'),
        1: require('@/assets/images/animation/bad/animation-01.svg')
      },
      image02: {
        0: require('@/assets/images/animation/default/animation-02.svg'),
        1: require('@/assets/images/animation/bad/animation-02.svg')
      },
      image03: {
        0: require('@/assets/images/animation/default/animation-03.svg'),
        1: require('@/assets/images/animation/bad/animation-03.svg')
      },
      image04: {
        0: require('@/assets/images/animation/default/animation-04.svg'),
        1: require('@/assets/images/animation/bad/animation-04.svg')
      },
      image05: {
        0: require('@/assets/images/animation/default/animation-05.svg'),
        1: require('@/assets/images/animation/bad/animation-05.svg')
      },
      image06: {
        0: require('@/assets/images/animation/default/animation-06.svg'),
        1: require('@/assets/images/animation/bad/animation-06.svg')
      },
      randomImg: [0, 0, 0, 0, 0, 0]
    }
  },
  props: {
    scorecard: Boolean,
    SummaryScores: Array,
    emcid: String
  },
  methods: {
    randoming() {
      // Ratio of 0 (defalut) and 1 (bad) for Grade D and F
      const resultD = [0, 0, 0, 1, 1, 1]
      const resultF = [0, 1, 1, 1, 1, 1]
      const randomResult = []
      // Render random combination of 0 and 1 in the ratio
      if(this.grade == "D") {
        for(let d=0; d<6;d++) {
          const randomD = resultD[Math.floor(Math.random() * resultD.length)];
          randomResult.push(randomD)
        }
      } else if (this.grade == "F") {
        for(let d=0; d<6;d++) {
          const randomF = resultF[Math.floor(Math.random() * resultF.length)];
          randomResult.push(randomF)
        }
      }
      this.randomImg = randomResult
    }
  },
  computed: {
    thisSummary() {
      return this.SummaryScores.filter(s=> {
        return s.id == this.emcid
      })
    },
    // Get the path to the image (svg)
    imgPath01() {
      if(this.grade == "D" || this.grade == "F") {
        if(this.randomImg[0] == 1)
        { return this.image01[1] }
        else { return this.image01[0] }
      } else {
        return this.image01[0]
      } 
    },
    imgPath02() {
      if(this.grade == "D" || this.grade == "F") {
        if(this.randomImg[1] == 1)
        { return this.image02[1] }
        else { return this.image02[0] }
      } else {
        return this.image02[0]
      } 
    },
    imgPath03() {
      if(this.grade == "D" || this.grade == "F") {
        if(this.randomImg[2] == 1)
        { return this.image03[1] }
        else { return this.image03[0] }
      } else {
        return this.image03[0]
      } 
    },
    imgPath04() {
      if(this.grade == "D" || this.grade == "F") {
        if(this.randomImg[3] == 1)
        { return this.image04[1] }
        else { return this.image04[0] }
      } else {
        return this.image04[0]
      } 
    },
    imgPath05() {
      if(this.grade == "D" || this.grade == "F") {
        if(this.randomImg[4] == 1)
        { return this.image05[1] }
        else { return this.image05[0] }
      } else {
        return this.image05[0]
      } 
    },
    imgPath06() {
      if(this.grade == "D" || this.grade == "F") {
        if(this.randomImg[5] == 1)
        { return this.image06[1] }
        else { return this.image06[0] }
      } else {
        return this.image06[0]
      } 
    }
  },
  watch: {
    scorecard(newV) {
      // When open new card, randomize it even if the grade is same
      if(newV == true) {
        this.grade = this.thisSummary[0].grade
        this.randoming()
      }
    }
  }
}
</script>

<style scoped>
  .blkbanner {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 250px;
    background: #000;
    opacity: 0;
    pointer-events: none;
  }
.onehouse, .house {
  display: block;
    position: fixed;
    bottom: -800px;
    height: 600px;
    transition-duration: 500ms;
    pointer-events: none;
}
.onehouse.three, #house2 {
  height: 700px;
}
/* .onehouse {
  overflow: hidden;
} */
#house1 { left: 0; }
#house2 { 
  /* left: 230px; */
  right: 150px;
   }
#house2.scoreon {
  bottom: -100px;
   }
#house3 { 
  /* right: 150px; */
  left: 230px;
   }
#house4 { right: 0; }
.scoreon {
  bottom: 0;
}
#house4.scoreon {
  bottom: 150px;
}
.onehouse img {
  position: fixed;
}
.window01 {
  width: 204px;
  bottom: 255px;
  left: 66px;
  opacity: 0;
}
.window02 {
  width: 70px;
  bottom: 363px;
  left: 128px;
  opacity: 0;
}
.window03 {
  width: 200px;
  /* bottom: 310px;
  left: 305px; */
  bottom: 260px;
  right: 280px;
  opacity: 0;
}
.window04 {
  width: 210px;
  /* bottom: 210px;
  right: 250px; */
  bottom: 210px;
  left: 335px;
  opacity: 0;
}
.window05 {
  width: 120px;
  bottom: 360px;
  right: 52px;
  opacity: 0;
}
.window06 {
  width: 60px;
  bottom: 503px;
  right: 90px;
  opacity: 0;
}
.scron { 
  opacity: 1;
  transition-duration: 200ms;
  transition-delay: 500ms;
}

@media only screen and (max-width: 1500px) {
#house1 {
  left:-60px;
}
#house2 {
  right:100px;
}
#house3 {
  left:0;
}
#house1.scoreon {
  bottom:130px;
}
.window01 {
  left: 10px;
  bottom: 385px;
}
.window02 {
  left: 65px;
  bottom: 495px;
}
.window03 {
  right: 230px;
}
.window04 {
  left: 105px;
}
}
@media only screen and (max-width: 1350px) {
#house2.scoreon {
  bottom:-100px;
}
#house2 {
  right:-70px;
}
#house3 {
  left:-60px;
}
#house1.scoreon {
  bottom:140px;
}
#house4 {
  right: -20px;
}
#house4.scoreon {
  bottom: 200px
}
.window01 {
  bottom: 390px;
}
.window02 {
  bottom: 500px;
}
.window03 {
  right: 60px;
}
.window04 {
  left: 45px;
  bottom: 210px;
}
.window05 {
  right: 35px;
  bottom: 410px;
}
.window06 {
  right: 70px;
  bottom: 553px;
}
  }
@media only screen and (max-width: 1024px) {
#house2.scoreon {
  bottom:-150px;
}
#house2 {
  right:-140px;
}
#house3 {
  left:-100px;
}
#house3.scoreon {
  bottom:-100px;
}
.window03 {
  right: -10px;
  bottom: 210px;
}
.window04 {
  left: 5px;
  bottom: 110px;
}
}
@media only screen and (max-width: 960px) {
#house1 {
  left:-100px;
}
#house2 {
  right:-160px;
}
#house3 {
  left:-120px;
}
#house4 {
  right: -50px;
}
.window01 {
  left: -20px;
}
.window02 {
  left: 25px;
}
.window03 {
  right: -30px;
}
.window04 {
  left: -15px;
}
.window05 {
  right: 5px;
}
.window06 {
  right: 40px;
}
}
@media only screen and (max-width: 960px) {
  #house3.scoreon {
  bottom:-150px;
}
.window04 {
  bottom: 60px;
}
}
</style>
