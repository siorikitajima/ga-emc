<template>
  <div class="scorecard" :class="{ scoreison: scorecard, carddelay: scorecard }">
    <div class="cardnav">
        <div class="emcname">{{ Score[0].name }}</div>
        <div class="icon leftarrow" @click="prevEMC()">
            <span>Prev EMC</span>
        </div>
        <div class="icon rightarrow" @click="nextEMC()">
            <span>Next EMC</span>
        </div>
        <div class="icon downloadbtn" @click="genPDF()">
            <span>Download PDF</span>
        </div>
        <div class="icon closebtn" @click="closeCard()">
            <span>Close ScoreCard</span>
        </div>
    </div>
    <div class="cardbody" ref="cardbody">
        <div class="geninfo" ref="geninfo">
            <h3 style="color: #FFF;">{{Score[0].name}}</h3>
            <p class="info">
            <a :href="Score[0].url" target="_blank" v-if="Score[0].url.includes('http')"><b>Website</b></a> / <a :href="Score[0].serviceMap" target="_blank" v-if="Score[0].serviceMap.includes('http')"><b>Service Area Map</b></a> / <a :href="Score[0].byLaws" target="_blank" v-if="Score[0].byLaws.includes('http')"><b>By-Laws</b></a>
            <br/>
            Customer population: <b>{{Poplation}}</b> people<br/>
            Counties served: <b class="tiles" v-for="(co, i) in EMCcounties[Score[0].id]" :key="'county' + i">{{co}} </b>
            </p>
        </div>
        <h3>// Score Summary //</h3>
        <div class="summary">
            <div class="totalrate" :class="SummaryScore[0].style">
                <p>Total Score: <b>{{ SummaryScore[0].total }}</b> / Grade: <b>{{ SummaryScore[0].grade }}</b></p>
            </div>
            <BarChart class="smrchart" :chartData="FixedAllTotals"  :options="options" :width="400" />
            <p class="caption">Comparison with other EMCs. {{ Score[0].name }} ranks <b>{{Suffix(theIndex+1)}}</b> in 41 EMCs.</p>

            <div class="dgscore catscore" @click="ScrollToDG()">
                <div class="square" :class="SummaryScore[0].dgStyle"></div>
                <p>Overall Democratic Governance: <b>{{ SummaryScore[0].dg }}</b> / 44</p>
            </div>
            <div class="fcscore catscore" @click="ScrollToFC()">
                <div class="square" :class="SummaryScore[0].fcStyle"></div>
                <p>Overall Finances and Compensation: <b>{{ SummaryScore[0].fc }}</b> / 20</p>
            </div>
            <div class="spscore catscore" @click="ScrollToPS()">
                <div class="square" :class="SummaryScore[0].psStyle"></div>
                <p>Overall Member Programs and Services: <b>{{ SummaryScore[0].ps }}</b> / 36</p>
            </div>
        </div>
        
        <Category ctgcontent = "Democratic Governance" :close="close" :questions = "DGQuestions" :scrs = "Score" ref="dg" />
        <FCCategory ctgcontent = "Finances and Compensation" :close="close" :questions = "FCQuestions" :scrs = "Score" ref="fc" :SummaryScore ="SummaryScore[0]" :emcid="emcid" />
        <Category ctgcontent = "Member Programs and Services" :close="close" :questions = "PSQuestions" :scrs = "Score" ref="ps" />
        <Footer :ThisQCta="ThisQCta" :ThisEmcCta="ThisEmcCta" />
    </div>
  </div>
</template>

<script>
import Category from './Category.vue';
import FCCategory from './FCCategory.vue';
import Footer from './Footer.vue';
import { Chart, registerables } from "chart.js";
import { BarChart } from 'vue-chart-3';
Chart.register(...registerables);

export default {
    name: "CardMain",
    data() {
        return {
            close: false
        }
    },
    props: {
        scorecard: Boolean,
        Questions: Array,
        emcid: String,
        Scores: Array,
        SummaryScores: Array,
        AllTotals: Object,
        EMCcounties: Array,
        EmcCta: Array,
        QCta: Array
    },
    components: { Category, FCCategory, BarChart, Footer },
    watch: {
        scorecard(newV) {
            this.close = !this.close
            if(newV == true) {
                this.$refs.geninfo.scrollIntoView();
            }
        }
    },
    computed: {
        Poplation() {
            return Number(this.Score[0].customers).toLocaleString("en-US")
        },
        DGQuestions() {
            return this.Questions.filter(q => q.category.includes('Democratic'))
        },
        FCQuestions() {
            return this.Questions.filter(q => q.category.includes('Finances'))
        },
        PSQuestions() {
            return this.Questions.filter(q => q.category.includes('Programs'))
        },
        Score() {
            let tempid = this.emcid
            return this.Scores.filter(s => s.id.includes(tempid))
        },
        SummaryScore() {
            let tempid = this.emcid
            return this.SummaryScores.filter(s => s.id.includes(tempid))
        },
        options() { //Option for the total Barchart
            let options = { 
                    plugins: {
                        legend: {
                            display: false
                        }
                    },
                    scales: {
                        x: {
                            grid: {
                            drawTicks: false
                            },
                            display: false
                        }
                        }
            }
            return options
        },
        theIndex() {
            let index
            this.AllTotals.labels.filter((t,i)=> {
                if(t == this.emcid) {
                    index = i
                }
            })
            return index
        },
        FixedAllTotals() { // Color for the Total Bar chart
            let newCol = []
            this.AllTotals.datasets[0].backgroundColor.forEach((b,i) => {
                if(i !== this.theIndex) { newCol.push("#666666")}
                else { newCol.push(b) }
            });
            let data = { 
                labels: this.AllTotals.labels,
                datasets: [
                {   data: this.AllTotals.datasets[0].data,
                    backgroundColor: newCol
            }]}
            return data
        },
        ThisQCta() { // CTAs rendered from low score for certain questions
            if(this.emcid) {
                let ctas = []
                this.QCta.forEach(q => {
                    let id = q.question
                    if ( this.Score[0][id] == 0) {
                        ctas.push(q)
                    } 
                })
                return ctas.slice(0,3)
            } else {
                return []
            }
        },
        ThisEmcCta() { // CTAs prepared for EMCs
            if(this.emcid) {
                return this.EmcCta.filter(e => {
                    return e.emc == this.emcid
                }) 
            } else {
                return []
            }
        }
    },
    methods: {
        closeCard() {
            this.$emit('closeCard')
        },
        genPDF() {
            this.$emit('genPDF')
        },
        nextEMC() {
            this.$emit('nextEMC')
        },
        prevEMC() {
            this.$emit('prevEMC')
        },
        Suffix(i) { // Ranking for the total Bar chart
            var j = i % 10,
                k = i % 100;
            if (j == 1 && k != 11) {
                return i + "st";
            }
            if (j == 2 && k != 12) {
                return i + "nd";
            }
            if (j == 3 && k != 13) {
                return i + "rd";
            }
                return i + "th";
        },
        ScrollToDG() { 
            this.$refs.dg.$refs.header.scrollIntoView({behavior: "smooth"});
        },
        ScrollToFC() { 
            this.$refs.fc.$refs.header.scrollIntoView({behavior: "smooth"});
        },
        ScrollToPS() { 
            this.$refs.ps.$refs.header.scrollIntoView({behavior: "smooth"});
        }
    }
}
</script>

<style scoped>
p a {
    color: #d3413f;
}
@media (hover: hover) {
    p a:hover {
        opacity: 0.6;
        transition-duration: 100ms;
    }
}
p.info {
    font-size: 0.9em;
    line-height: 1.7em;
}
p.info b.tiles {
    margin-right: 10px;
    word-wrap: normal;
    display: inline-block;
}
  .scorecard {
    height: calc(100% - 60px);
    width: 600px;
    max-width: 100%;
    padding: 0;
    position: fixed;
    bottom: -110%;
    left: 50%;
    transition-duration: 350ms;
    transition-delay: 150ms;
    transform: translateX(-50%);
    background: #efefef;
    color: #231F20;
    z-index: 1000;
  }
  h3 {
        margin: 30px auto 0 auto;
        color: #AAA;
    }

    .scoreison {
     bottom: 0;
    }

    .cardnav {
        height: 50px;
        width: 100%;
        background: #DDD;
        border-bottom: #AAA 1px solid;
        position: relative;
        z-index: 1200;
    }
    .cardnav div {
        display: inline-block;
        position: absolute;
        height: 40px;
        margin: 5px 10px;
    }
    .cardnav div.icon {
        width: 40px;
        height: 40px;
        border-radius: 5px;
        cursor: pointer;
    }
    .cardnav div.icon span {
        position: absolute;
        bottom: calc(-100% - 10px);
        left: 50%;
        transform: translateX(-50%);
        background: #111;
        color: #AAA;
        padding: 5px 10px;
        border-radius: 5px;
        display: none;
    }
    
    @media (hover: hover) {
        .cardnav div.icon:hover span {
            display: block;
        }
    }
    .leftarrow {
        background-image: url('../../assets/images/card/leftarrow.svg');
        left: 10px;
    }
    .rightarrow {
        background-image: url('../../assets/images/card/rightarrow.svg');
        left: 60px;
    }
    .downloadbtn {
        background-image: url('../../assets/images/card/download.svg');
        right: 60px;
    }
    .closebtn {
        background-image: url('../../assets/images/card/closebtn.svg');
        right: 10px;
    }
    .emcname {
        position: absolute;
        width: calc(100% - 20px);
        left: 0;
        padding-top: 10px;
        font-weight: bold;
        font-size: 0.9em;
    }
    .cardbody {
        height: inherit;
        overflow-y: auto;
    }
    .summary {
        width: calc(100% - 40px);
        padding: 10px 20px;
    }
    .geninfo {
        width: calc(100% - 80px);
        padding: 10px 40px;
        background: #231F20;
        color: #DDD;
    }
    .summary div {
        width: calc(100% - 20px);
        padding: 10px;
        margin: 5px 0;
    }
    .summary div p {
        margin:0;
    }

    .rank-a {background:#03E538;}
	.rank-b {background:#A8FF00;}
	.rank-c {background:#fffb00;}
	.rank-d {background:#FF9100;}
	.rank-f {background:#FF3939;}

    .summary div.smrchart {
        width: 100%;
        height: 210px;
        margin: 0;
        padding: 0;
        position: relative;
    }
    h3.charttitle {
        background: #FFF;
        margin: 0;
        padding: 5px 0;
        font-size: 1em;
    }
    .caption {
        margin: 0 0 10px 0;
        font-size: 0.9em;
    }
    div.catscore {
        background: #FFF;
        position: relative;
        padding: 10px 10px 10px 20px;
        width: calc(100% - 30px);
        text-align: left;
        cursor: pointer;
    }
    @media (hover: hover) {
        div.catscore:hover {
            background: #d3413f;
            color: #FFF;
            transition-duration: 200ms;
        }
    }
    .catscore p {
        display: block;
        font-size: 0.9em;
    }
    .catscore .square {
        width: 10px;
        height: 100%;
        margin: 0;
        padding: 0;
        position: absolute;
        top: 0;
        left: 0;
    }
    
    @media (max-width: 780px) {
        .carddelay {
            transition-delay: 2000ms;
        }
        .scorecard {
            height: calc(100% - 0px);
        }
        .cardbody {
            height: calc(100% - 60px);
        }
        div.emcname {
            display: none;
        }
    }
</style>
