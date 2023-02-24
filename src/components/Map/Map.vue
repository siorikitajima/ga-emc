<template>
  <div class="container">

    <div class="emcmaps">
      <EmcSvg :emcLHover="emcLHover" :emcLId="emcLId" :countyLHover="countyLHover" :countyLId="countyLId" 
      :SummaryScores="SummaryScores" @data-emc="hoverEmc" @data-cnt="hoverCnt" @hover="hoverState" @open-emc="openScoreCard" :toggleCounty="toggleCounty" :showList="showList" class="mapImg" />
    </div>

    <div class="emcbox" ref="emcbox" v-if="hover && !scorecard && !toggleCounty">
      <div class="closebtn" @click="closePP"></div>
      <h3>{{ theEmc.name }}</h3>
      <div>
        <div class="summary total" ref="totalbox" :class="emcCol"><p>Total Score: <b>{{ theSammary.grade }}</b></p></div>
        <div class="summary catg">
          <div class="square" :class="theSammary.dgStyle"></div>
          <p>Overall Governance: <b>{{ theSammary.dg }}</b> / 44</p>
        </div>
        <div class="summary catg">
          <div class="square" :class="theSammary.fcStyle"></div>
          <p>Overall Finances: <b>{{ theSammary.fc }}</b> / 20</p>
        </div>
        <div class="summary catg">
          <div class="square" :class="theSammary.psStyle"></div>
          <p>Overall Services: <b>{{ theSammary.ps }}</b> / 36</p>
        </div>
      </div>
      <p class="EmcInfo">Customers Served: <b>{{ Number(theSammary.customers).toLocaleString("en-US") }}</b><br/>Counties: 
        <b class="tile" v-for="c in theSammary.counties" :key="c">{{c}} </b>
      </p>
    </div>

    <CountyPP v-if="toggleCounty && hover && !scorecard" :theCnt="theCnt" :racialRatio="racialRatio" :incomeRatio="incomeRatio" :CountyEmcs="CountyEmcs" :SummaryScores="SummaryScores" class="emcbox racial" @openEmc="openScoreCard" @closePP="closePP" :headerIcon="false" />

    <div class="legend emc" v-if="!toggleCounty && !scorecard">
      <img src="../../assets/images/map/legend-emc.svg" alt="legend emc">
    </div>
    <div class="legend county" v-if="toggleCounty && !scorecard">
      <img src="../../assets/images/map/legend-county.svg" alt="legend county">
    </div>
    <div class="legend noemc" v-if="!scorecard">
      <img src="../../assets/images/map/no-emc.svg" alt="legend noemc">
    </div>
  </div>
</template>

<script>
import EmcSvg from './EmcSvg.vue'
import CountyPP from '@/components/Lists/CountyPP.vue'

export default {
  components: { EmcSvg, CountyPP },
  name: 'MapMap',
  data() {
    return {
      hover: false,
      emc: '',
      county: ''
    }
  },
  props: {
    emcLHover: Boolean,
    emcLId: String,
    countyLHover: Boolean,
    countyLId: String,
    scorecard: Boolean,
    Counties: Array,
    EMCs: Array,
    SummaryScores: Array,
    toggleCounty: Boolean,
    showList: Boolean,
    racialRatio: Array,
    incomeRatio: Object,
    theCnt: Object,
    CountyEmcs: Array
  },
  computed: {
    theEmc() {
      if(this.emc.length > 0) {
        let result = this.EMCs.filter(emc => emc.id == this.emc)
        if(result.length < 1) { 
          return '' }
        else { 
          return result[0] }
      } else {
        return ''
      }
    },
    theSammary() {
      let aSummary = { "total" : 58, "rate" : "C", "dg" : 28, "fc" : 10, "ps" : 20 }
      if(this.emc.length > 0) {
        let result = this.SummaryScores.filter(emc => emc.id == this.emc)
        if(result.length < 1) { 
            return aSummary }
        else { 
            return result[0] }
      } else {
        return aSummary
      }
    },
    emcCol() {
      let sty = this.SummaryScores.filter(emc => emc.id == this.emc)
      if(sty.length > 0) {
         return sty[0].style
      } else {
         return 'rank-c'
      }
    },
  },
  methods: {
    openScoreCard(v) {
      this.$emit('openScoreCard', v)
      this.hover = false
    },
    hoverEmc(v) {
      if(!this.$isMobile()) {
        this.emc = v
      }
    },
    hoverCnt(v) {
      if(!this.$isMobile()) {
        this.county = v
        this.$emit('hoverCnt', v)
      }
    },
    hoverState(v) {
      if(v == true && !this.$isMobile()) {
        this.hover = v
      }
    },
    closePP() {
      this.hover = false
    }
  },
  watch: {
    toggleCounty() {
      this.hover = false
    },
    emcLId(newV) {
      this.emc = newV
    },
    countyLId(newV) {
      this.county = newV
    },
    emcLHover(newV) {
      if(newV == true && !this.$isMobile()) {
        this.hover = true
      }
    },
    countyLHover(newV) {
      if(newV == true && !this.$isMobile()) {
        this.hover = true
      }
    }
  }
}
</script>

<style scoped>
  .maps {
    width: 810px;
    height: 900px;
    position: fixed;
    margin: 20px auto 0 auto;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: none;
  }

  .emcmaps {
    width: calc(100% - 40px);
    height: calc(100% - 40px);
    position: fixed;
    margin: 20px;
    top: 0;
    left: 0;
  }
  .emcbox {
    position: fixed;
    background: #FFF;
    top: 110px;
    right: 20px;
    width: 260px;
    padding: 18px;
    color: #111;
    border: #111 1px solid;
    font-size: 0.9em;
    z-index: 100;
  }
  .emcbox .summary {
    padding: 5px;
    margin: 5px 0;
  }
  .rank-a { background: #03E538; }
  .rank-b { background: #A8FF00; }
  .rank-c { background: #fffb00; }
  .rank-d { background: #FF9100; }
  .rank-f { background: #FF3939; }
  .emcbox .catg {
    background: #eee;
    position: relative;
  }
  .emcbox .catg .square {
    width: 10px;
    height: 100%;
    margin: 0;
    padding: 0;
    position: absolute;
    top: 0;
    left: 0;
  }
  .emcbox.racial {
    background: #FFF;
    color: #111;
  }
  .emcbox h3 {
    margin: 10px 0 5px 0;
    padding: 0;
  }
  .emcbox p {
    margin: 0;
  }
  .emcbox .closebtn {
        width: 40px;
        height: 40px;
        border-radius: 5px;
        cursor: pointer;
        position: absolute;
        background-image: url('../../assets/images/card/closebtn.svg');
        right: 0;
        top: 0;
    }
  .legend {
    width: 300px;
    text-align: right;
    margin: -30px 20px 0 auto;
    pointer-events: none;
  }
  .legend.noemc {
    position: absolute;
    bottom: 100px;
    right: 0;
  }
  .mapImg {
    width: 648px;
    /* height: fit-content; */
    max-height: calc(100% - 150px);
    max-width: 100%;
    /* position: fixed; */
    margin: 20px auto;
    /* top: 50%;
    left: 50%;
    transform: translate(-50%, -50%); */
  }

  .EmcInfo {
    margin: 10px 0 0 0;
    line-height: 1.5em;
  }
  .tile {
    margin: 0 5px 0 0;
    display: inline-block;
  }

  @media only screen and (max-height: 700px) {
        .mapImg {
        height: calc(100% - 50px);
        }
        .emcbox {
          z-index: 200;
        }
    }
  @media only screen and (max-width: 900px) {
    .mapImg {
        margin: 50px auto 0 auto;
    }
    .legend {
      width: 200px;
      margin: 60px auto 0 auto;
      padding-left: 350px;
    }
    .legend.noemc {
      position: static;
      bottom: unset;
      right: unset;
      margin: 0 auto;
    }
    @media only screen and (max-height: 700px) {
        .mapImg {
          margin: 40px auto 0 auto;
        height: calc(100% - 100px);
        }
    }
  }
  @media only screen and (max-width: 600px) {
    .mapImg {
          margin: 40px auto 0 auto;
          width: calc(100% - 0px);
        }
    .legend {
      margin: none;
      padding-left: none;
      position: fixed;
      right: 20px;
    }
    .legend.noemc {
      position: fixed;
      top: 160px;
      right: 20px;
      margin: none;
    }
  }
  @media only screen and (max-width: 460px) {
    .mapImg {
          margin: 40px auto 0 auto;
          width: calc(100% - 20px);
        }
    .legend {
      right: 10px;
    }
  }
</style>
