<template>
<div>
    <!-- for Screen -->
    <div class="wrapper" v-if="loaded && !pdf">
        <div class="ctgitem" @click="accordion()">
            <div class="ctgname">
                <img :class="{desc: desc}" src="../../assets/images/card/plus.svg"/>
                {{ question.question }}
            </div>
            <div class="score" v-if="(question.highestscore !== 'neutral')">
                <b>{{ ScoreVal.score }}</b> / {{question.highestscore}}</div>

            <div class="rate" v-if="question.highestscore !== 'neutral'">
                <img class="rateicon" src="../../assets/images/card/bad.svg" alt="bad" v-show="(ScoreVal.score == 0)">
                <img class="rateicon" src="../../assets/images/card/good.svg" alt="good" v-show="(ScoreVal.score == question.highestscore)">
            </div>            
        </div>
        <div class="ctgdetail" v-if="desc">
            <div class="desc"><b>Expectation:</b> {{ question.description }}</div>
            <div class="desc" v-if="((question.highestscore == null || question.highestscore == 'neutral'))" style="margin: 2px 0 0 0;"><b>Current status: </b>{{ ScoreNum }}</div>
            <div class="desc" v-else-if="question.id == 'fc-05' || question.id == 'fc-10'" style="margin: 2px 0 0 0;"><b>Current status: </b>{{ ScoreNum }}</div>
            <div class="desc" v-else style="margin: 2px 0 0 0;"><b>Current status: </b>{{ ScoreVal.value }}</div>

            <DoughnutChart class="chart donutChart" :chartData="donutData" :options="donutOptions" :width="400" v-if="scoresys.length > 0"/>
        </div>

    </div>

    <!-- for PDF -->
    <div class="wrapper pdf" :class="{nestclas: nestclas}" v-if="loaded && pdf">
        <div class="left">
            <div class="ctgitem pdf" @click="accordion()">
                <div class="ctgname" style="font-family:'Open Sans', Helvetica;">{{ question.question }}</div>
                <div class="score" v-if="(question.highestscore !== 'neutral')" style="font-family:'Open Sans', Helvetica;"><b>{{ ScoreVal.score }}</b> / {{question.highestscore}}</div>

                <div class="rate" v-if="question.highestscore !== 'neutral'">
                    <img class="rateicon" src="../../assets/images/card/bad.png" alt="bad" v-show="(ScoreVal.score == 0)">
                    <img class="rateicon" src="../../assets/images/card/good.png" alt="good" v-show="(ScoreVal.score == question.highestscore)">
                </div>            
            </div>
            <div class="ctgdetail pdf">
                <div class="desc" style="font-family:'Open Sans', Helvetica;"><b>Expectation:</b> {{ question.description }}</div>
                <div class="desc" v-if="((question.highestscore == null || question.highestscore == 'neutral'))" style="margin: -10px 0 0 0; font-family: 'Open Sans', Helvetica;"><b>Current status:</b> {{ ScoreNum }}</div>
                <div class="desc" v-else-if="question.id == 'fc-05' || question.id == 'fc-10'"  style="margin: -10px 0 0 0; font-family: 'Open Sans', Helvetica;"><b>Current status: </b>{{ ScoreNum }}</div>
                <div class="desc" v-else style="margin: -10px 0 0 0; font-family: 'Open Sans', Helvetica;"><b>Current status:</b> {{ ScoreVal.value }}</div>
            </div>
        </div>
        
        <div class="right" v-if="question.highestscore !== 'neutral'">
            <DoughnutChart class="chart donutChart" :chartData="donutData" :options="donutOptionsPdf" :width="400" v-if="scoresys.length > 0"  style="font-family: 'Open Sans', Helvetica;"/>
            <ul v-if="scoresys.length > 0" style="text-align: left; font-size: 10px;">
                <li v-for="(sys, i) in scoresys" :key="sys.value" :style="'border-left: 6px solid ' + theColors[i] + ';'" style="font-family: 'Open Sans', Helvetica;">{{ sys.value}}</li>
            </ul>
        </div>
    </div>
</div>
</template>

<script>
import { Chart, registerables } from "chart.js";
import { DoughnutChart } from 'vue-chart-3';
Chart.register(...registerables);


export default {
  name: 'CardQuestion',
  components: { DoughnutChart },
  data() {
    return {
        desc: false,
        loaded: false
    }
  },
  props: {
    question: Object,
    scr: Number,
    scoresys: Array,
    pdf: Boolean,
    SummaryScore: Object,
    emcid: String,
    close: Boolean,
    nestclas: Boolean
  },
    methods: {
        accordion() {
            this.desc = !this.desc
        },
        resetQ() {
            this.desc = false
        }
    },
    computed: {
        ScoreVal() {
            if(this.question.id == 'fc-05') {
                if(this.scr < 50) { return {score: 6 }}
                else { return {score:0 } }
            } else if(this.question.id == 'fc-10') {
                if(this.scr < 5) { return {score: 6 }}
                else if (this.scr < 7) { return {score: 3}}
                else { return {score:0 } }
            } else if(this.scoresys.length > 0) {
                let sc = this.scoresys.filter(c => c.score == this.scr)
                return sc[0]
            } else {
                return {score: null, value: ''}
            }
        },
        ScoreNum() {
            if(this.question.scoreunit) {
                return this.scr.toLocaleString("en-US") + ' ' + this.question.scoreunit
            } else {
                return this.scr
            }
        },
        donutData() {
            let points = []
            let labels = []
            let color = []
            if(this.question.highestscore !== null) {
                this.scoresys.forEach(i => {
                    labels.push(i.value)
                    points.push(i.totals)
                })
                if(labels.length == 2) {
                    color[0] = '#03E538'
                    color[1] = '#FF3939'
                } else if(labels.length == 3) {
                    color[0] = '#03E538'
                    color[1] = '#fffb00'
                    color[2] = '#FF3939'
                } else if(labels.length == 4) {
                    color[0] = '#03E538'
                    color[1] = '#fffb00'
                    color[2] = '#FF9100'
                    color[3] = '#FF3939'
                } else if(labels.length == 5) {
                    color[0] = '#03E538'
                    color[1] = '#A8FF00'
                    color[2] = '#fffb00'
                    color[3] = '#FF9100'
                    color[4] = '#FF3939'
                }
            }
            let data = { 
                labels: labels,
                datasets: [
                {   data: points,
                    backgroundColor: color
                }]}
            return data
        },
        theColors() {
            let color = []
            if(this.question.highestscore !== null) {
                if(this.scoresys.length == 2) {
                    color[0] = '#03E538'
                    color[1] = '#FF3939'
                } else if(this.scoresys.length == 3) {
                    color[0] = '#03E538'
                    color[1] = '#fffb00'
                    color[2] = '#FF3939'
                } else if(this.scoresys.length == 4) {
                    color[0] = '#03E538'
                    color[1] = '#fffb00'
                    color[2] = '#FF9100'
                    color[3] = '#FF3939'
                } else if(this.scoresys.length == 5) {
                    color[0] = '#03E538'
                    color[1] = '#A8FF00'
                    color[2] = '#fffb00'
                    color[3] = '#FF9100'
                    color[4] = '#FF3939'
                }
            }
            return color
        },
        options() {
            let axisUnit, tooltipUnit
            if(this.question.id == 'fc-05') {
                axisUnit = '%'
                tooltipUnit = '%'
            } else {
                axisUnit = 'x'
                tooltipUnit = ' times'
            }
            let options = { 
                    plugins: {
                        legend: {
                            display: false
                        },
                        tooltip: {
                            intersect: false,
                            cornerRadius: 4,
                            caretSize: 4,
                            xPadding: 16,
                            yPadding: 10,
                            titleFontStyle: 'normal',
                            titleMarginBottom: 15,
                            callbacks: {
                                label: function(context) {
                                    let label = context.dataset.label || '';
                                    if (label) {
                                        label += ': ';
                                    }
                                    if (context.parsed.y !== null) {
                                        label += context.parsed.y + tooltipUnit;
                                    }
                                    return label;
                                }
                            }
                        }
                    },
                    scales: {
                        x: {
                            grid: {
                                drawTicks: false
                            },
                            display: false
                        },
                        y: {
                            ticks: {
                                callback: function(value) {
                                    return value + axisUnit;
                                }
                            }
                        }
                        }
            }
            return options
        },
        donutOptions() {
            let options = { 
                responsive: true,
                plugins: {
                    legend: {
                        display: true,
                        position: 'top',
                        align: 'center',
                        onClick: null,
                        labels: {
                            boxWidth: 12
                        }
                    }
                },
                layout: {
                    padding: {
                        top: 30,
                        bottom: 0
                    },
                }
            }
            return options  
        },
        donutOptionsPdf() {
            let options = { 
                responsive: true,
                plugins: {
                    legend: {
                        display: false
                    }
                },
                layout: {
                    padding: {
                        top: 0,
                        bottom: 0
                    },
                }
            }
            return options  
        }
    },
    mounted() {
        this.loaded = true
        this.desc = false
    },
    watch: {
        close() {
            this.resetQ()
        }
    }
}
</script>

<style scoped>
    .wrapper {
        height: fit-content;
        font-size: 14px;
    }
    .wrapper.pdf {
        display: flex;
        gap:2px;
        break-inside: avoid;
    }
    .wrapper.pdf.nestclas {
        display: block;
        gap: unset;
    }
    .wrapper.pdf .left {
        width: inherit;
        min-width: calc(100% - 285px);
        flex-shrink: 2;
        height: 100%;
    }
    .wrapper.pdf .right {
        width: 270px;
        flex-shrink: 0;
        background: #efefef;
        margin-bottom: 5px;
        padding: 5px;
        overflow: hidden;
        display: flex;
    }
    .wrapper.pdf .right ul {
        padding: 0 10px;
        list-style: none;
        margin: 5px 0;
        flex-shrink: 2;
    }
    .wrapper.pdf .right ul li {
        margin-top: 5px;
        padding-left: 10px;
    }
    .ctgitem {
        display: flex;
        width: 100%;
        gap: 2px;
        margin-bottom: 5px;
    }
    .ctgitem.pdf {
        margin-bottom: 0;
        width: calc(100% - 8px);
        border-left: #ccc 8px solid;
    }
    .ctgitem div {
        background: #FFF;
        padding: 6px 10px;
    }
    .ctgitem.pdf div {
        padding: 3px 10px 1px 10px;
    }
    @media (hover: hover) {
        .ctgitem:hover div {
            background: #d3413f;
            /* color: #FFF; */
            transition-duration: 200ms;
        }
    }
    .ctgitem div.score {
        width: 50px;
    }
    .ctgitem div.rate {
        width: 20px;
        flex-shrink: 0;
    }
    .ctgitem div.ctgname {
        width: inherit;
        text-align: left;
        color: #231F20;
        padding: 10px 10px 10px 20px;
        display: flex;
        justify-content: left;
    }
    .ctgitem div.ctgname img {
        width: 12px;
        margin-right: 10px;
        display: inline-block;
    }
    .ctgitem div.ctgname img.desc {
        transform-origin: center center;
        transform: rotate(45deg);
        transition-duration: 100ms;
    }
    .ctgdetail div.desc {
        text-align: left;
        padding: 10px 10px 10px 20px;
        margin: 5px 0 0 0 ;
        background: #FFF;
    }
    .ctgitem.pdf div {
        background: #efefef;
    }
    .ctgitem.pdf div.ctgname {
        padding: 8px 10px 5px 10px;
        font-weight: bold;
    }
    .ctgitem.pdf div.rate {
        padding: 5px 10px 5px 10px;
    }
    .ctgdetail.pdf div.desc {
        padding: 5px 10px;
        margin: 0;
        font-size: 12px;
        color: #444444;
        background:none;
        overflow: visible;
    }
    .ctgdetail {
        margin: -3px 0 10px 0;
        color: #888;
        border-left: #efefef 20px solid;
        /* font-size: 0.9em; */
    }
    .ctgdetail.pdf {
        margin: 0;
        border-left: #ffffff 8px solid;
    }
    .chart {
        height: fit-content;
        position: relative;
    }
    .descIsOn {
        opacity: 1;
        transition-delay: 200ms;
    }
    .caption {
        margin: 5px 0;
    }
    .rateicon {
        display: block;
        width: 26px;
        height: 26px;
        margin: 0;
        padding: 0;
    }
    .cdc-legend-item {
        margin: 3px !important;
    }
    .barChart {
        width: calc(100% - 30px);
        height: 135px;
        background: #FFF;
        margin: 3px 0 0 0;
        padding: 30px 10px 0 20px;
    }
    .donutChart {
        width: calc(100% - 30px);
        height: 200px;
        background: #FFF;
        margin: 3px 0 0 0;
        padding: 0 10px 5px 20px;
        text-align: left;
        overflow: visible;
        position: relative;
    }

    .barChart::after, .donutChart::after {
        content: "Comparison of all 41 EMCs";
        font-weight: bold;
        position: absolute;
        top: 5px;
        left: 20px;
        color: #888;
    }
    .donutChart::after {
        content: "Comparison of all 41 EMCs";
    }
    .pdf .barChart::after, .pdf .donutChart::after {
        display: none;
    }
    .pdf .donutChart {
        padding: 5px 20px 0 20px;
        width: 60px;
        /* max-width: 400px; */
        height: 60px;
        background: #efefef;
        flex-shrink: 0;
        overflow: visible;
    }
    .pdf .barChart {
        height: 90px;
        padding: 0 10px;
        background: #efefef;
    }
</style>
