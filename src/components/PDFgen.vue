<template>
<div id="allpage" style="font-family: 'Open Sans', Helvetica, Arial, sans-serif;">
    <div class="pdfwrap" id="pdfwrap">
        <div id="page1">
            <div class="header">
                <p style="text-align: center; font-family: 'Open Sans', Helvetica;">GEORGIA EMC SCORECARD #{{(theIndex +1)}} <br/>by <a href="https://billsaretoohigh.com/">BillsAreTooHigh.com</a></p>
                <h1 style="text-align: center; font-family: 'Open Sans', Helvetica;">{{ Score[0].name }}</h1>
                <p class="info" style="text-align: center; font-family:'Open Sans', Helvetica;">
                    Links: <a :href="Score[0].url" v-if="Score[0].url.includes('http')"><b>Website</b></a> / <a :href="Score[0].serviceMap" v-if="Score[0].serviceMap.includes('http')"><b>Service Area Map</b></a> / <a :href="Score[0].byLaws"><b v-if="Score[0].byLaws.includes('http')">By-Laws</b></a><br/>
                    Customer population: <b>{{Poplation}}</b> people<br/>
                    Counties served: <b class="tiles" v-for="(co, i) in EMCcounties[Score[0].id]" :key="'county' + i">{{co}} </b>
                    </p>
            </div>
            <div class="cardbody">
                <div class="map">
                    <EmcSvg :pdfHover="Score[0].id" :SummaryScores="SummaryScores" class="mapImg" />
                    <img src="../assets/images/map/legend-emc.jpg" alt="" class="legend">
                    <img src="../assets/images/map/no-emc.jpg" alt="" class="legend">
                </div>
                
                <div class="summary">
                    <h3 style="text-align: center; font-family: 'Open Sans', Helvetica;">// Score Summary //</h3>
                    <div class="totalrate" :class="SummaryScore[0].style" style="text-align: center; font-family: 'Open Sans', Helvetica;">
                        <p>Total Score: <b>{{ SummaryScore[0].total }}</b> / Grade: <b>{{ SummaryScore[0].grade }}</b></p>
                    </div>
                    <BarChart class="smrchart" :chartData="FixedAllTotals"  :options="options" />
                    <p class="caption" style="text-align: center; font-family: 'Open Sans', Helvetica;">Comparison with other EMCs. {{ Score[0].name }} ranks <b>{{Suffix(theIndex+1)}}</b> in 41 EMCs.</p>
                    
                    <div class="dgscore catscore">
                        <div class="square" :class="SummaryScore[0].dgStyle"></div>
                        <p style="font-family: 'Open Sans', Helvetica;">Overall Democratic Governance: <b>{{ SummaryScore[0].dg }}</b> / 44</p>
                    </div>
                    <div class="fcscore catscore">
                        <div class="square" :class="SummaryScore[0].fcStyle"></div>
                        <p style="font-family: 'Open Sans', Helvetica;">Overall Finances and Compensation: <b>{{ SummaryScore[0].fc }}</b> / 20</p>
                    </div>
                    <div class="spscore catscore">
                        <div class="square" :class="SummaryScore[0].psStyle"></div>
                        <p style="font-family: 'Open Sans', Helvetica;">Overall Member Programs and Services: <b>{{ SummaryScore[0].ps }}</b> / 36</p>
                    </div>
                </div>
            </div>
        </div>
        
        <div class="categories" id="categories">
            <Category ctgcontent = "Democratic Governance" :questions = "DGQuestions" :scrs = "Score" :pdf="true" class="ctg" :emcid = "emcid" id="category1"  />
            <FCCategory ctgcontent = "Finances and Compensation" :questions = "FCQuestions" :scrs = "Score" :emcid="emcid" :pdf="true" :SummaryScore ="SummaryScore[0]" class="ctg" id="category2" />

            <PDFCategory01 ctgcontent = "Member Programs and Services" :questions = "PSQuestions" :scrs = "Score" :pdf="true" class="ctg" :emcid = "emcid" id="category3"  />
            <PDFCategory02 ctgcontent = "Member Programs and Services" :questions = "PSQuestions" :scrs = "Score" :pdf="true" class="ctg" :emcid = "emcid" id="footer" :ThisQCta="ThisQCta" :ThisEmcCta="ThisEmcCta" />
        </div>
    </div>
    <div class="modal" v-if="modal">{{message}}</div>
</div>
</template>

<script>
import html2pdf from 'html2pdf.js';
import FCCategory from './Card/FCCategory.vue';
import Category from './Card/Category.vue';
import EmcSvg from './Map/EmcSvg.vue';
import { Chart, registerables } from "chart.js";
import { BarChart } from 'vue-chart-3';
import PDFCategory01 from './Card/PDFCategory01.vue';
import PDFCategory02 from './Card/PDFCategory02.vue';
Chart.register(...registerables);

export default {
    name: "PDFgen",
    data() {
        return {
            modal: true,
            message: 'Downloading...'
        }
    },
    props: {
        scorecard: Boolean,
        Questions: Array,
        emcid: String,
        Scores: Array,
        SummaryScores: Array,
        EMCcounties: Array,
        AllTotals: Object,
        EmcCta: Array,
        QCta: Array
    },
    components: { Category, FCCategory, EmcSvg, BarChart, PDFCategory01, PDFCategory02 },
    mounted() {
        setTimeout(() => {
            this.exportToPDF()          
        }, 2000)
    },
    methods: {
        Suffix(i) {
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
        async exportToPDF() {
            let page1 = document.getElementById("page1");
            let category1 = document.getElementById("category1");
            let category2 = document.getElementById("category2");
            let category3 = document.getElementById("category3");
            let footer = document.getElementById("footer");
            let pages = [page1, category1, category2, category3, footer]

                let option = {
				// margin: 0.125,
                filename: this.Score[0].name + "-scorecard.pdf",
                image: { type: 'jpeg', quality: 1 },
                html2canvas: {
                scale: 1.5,
                dpi: 192,
                letterRendering: true,
                allowTaint: true
                },
                jsPDF: { 
                    unit: "mm",
                    format: [300, 260], // 2267, 1965 in px
                    orientation: 'portrait',
                    compress: true },
                enableLinks: true
                }

                let worker = html2pdf().set(option)
                .from(pages[0])
                .toPdf();
                await pages.slice(1).forEach(page => {
                    worker = worker.get('pdf').then(pdf => {
                        pdf.addPage();
                    }).set(option).from(page)
                    .toContainer()
                    .toCanvas()
                    .toPdf();
                })
                worker = worker.save().then(()=>{
                    this.modal = false
                });
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
        theIndex() {
            let index
            this.AllTotals.labels.filter((t,i)=> {
                if(t == this.emcid) {
                    index = i
                }
            })
            return index
        },
        FixedAllTotals() {
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
        options() {
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
        ThisQCta() {
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
        ThisEmcCta() {
            if(this.emcid) {
                return this.EmcCta.filter(e => {
                    return e.emc == this.emcid
                }) 
            } else {
                return []
            }
        }
    },
    watch: {
        modal(newV) {
            if(newV == false) {
                this.$emit('closePDF') 
            }
        }
    }
}
</script>

<style scoped>
#allpage {
    width: 100%;
    height: 100%;
    position: fixed;
    background: #FFF;
    top: 0;
    left: 0;
    z-index: 10000;
}
@keyframes modalmove {
    0% { transform: scale(100%);}
    50% { transform: scale(110%);}
    100% { transform: scale(100%);}
}
.modal {
    position: fixed;
    z-index: 7000;
    width: 200px;
    height: 110px;
    border-radius: 50%;
    color: #FFF;
    padding-top: 90px;
    background: #231F20;
    font-weight: bold;
    left: calc(50% - 100px);
    top: calc(50% - 100px);
    transform-origin: center center;
    animation: modalmove 2s infinite ease-in-out;
}
#pdfdlbtn {
    width: 100px;
    height: 40px;
    z-index: 6000;
    position: fixed;
    top: 20px;
    right: 20px;
}
.pdfwrap {
    width: 100%;
    height: fit-content;
    /* width: 1965px;
    height: 2267px; */
    max-width: 80%;
    position: absolute;
    top: 0;
    left: 50%;
    transform: translateX(-50%);
    z-index: 5000;
    background: #FFF;
}
.header {
    background: #231F20;
    color: #ddd;
    padding: 20px 0;
    margin: 0 0 20px 0;
}
    .cardbody {
        height: fit-content;
        width: 100%;
        margin: 0 auto;
        display: flex;
    }
    .map {
        width: calc(50% - 20px);
        padding-top: 60px;
    }
    img.legend {
        width: 280px;
        max-width: 100%;
        display: block;
    }
    .mapImg {
        width: 90%;
        margin: 0 auto;
    }
    .summary {
        width: calc(50% - 40px);
        /* display: flex; */
        padding: 10px 20px;
        gap: 5px;
        justify-content: space-evenly;
    }
    .summary div {
        width: calc(100% - 20px);
        padding: 10px;
        margin: 5px 0;
        position: relative;
    }
    .summary div p {
        margin:0;
    }
    .summary div.smrchart {
        width: 100%;
        height: 150px;
        margin: 0 0 10px 0;
        padding: 0;
        position: relative;
    }
    .summary div.smrchart p {
        font-size: 0.9em;
        /* margin-bottom: 20px; */
    }
    .categories {
        padding: 0 20px;
    }
    p a {
    color: #d3413f;
    }

    p.info {
        max-width: 80%;
        margin: 0 auto 20px auto;
        font-size: 0.9em;
        line-height: 1.7em;
    }
    p.info b.tiles {
        margin-right: 10px;
        word-wrap: normal;
        display: inline-block;
    }
    .rank-a {background:#03E538;}
	.rank-b {background:#A8FF00;}
	.rank-c {background:#fffb00;}
	.rank-d {background:#FF9100;}
	.rank-f {background:#FF3939;}


    .caption {
        margin: 0 0 30px 0;
        font-size: 0.9em;
    }
    div.catscore {
        background: #eee;
        position: relative;
        padding: 10px 10px 10px 20px;
        width: calc(100% - 30px);
        text-align: left;
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
</style>