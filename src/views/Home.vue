<template>
    <div class="home" :class="{ sky: scorecard, pdf: PDF }">
        <div class="header">

            <div class="lists">
                <div class="toggle">
                    <div class="btn" :class="{ hovering: !toggleCounty}" v-if="!scorecard" @click="emcScreenSwitch">
                        <p>EMCs</p>
                    </div>
                    <div class="btn" :class="{ hovering: toggleCounty}" v-if="!scorecard" @click="countyScreenSwitch">
                        <p>Counties</p>
                    </div>
                </div>
                <h1 :class="{ light: scorecard}">GEORGIA EMC SCORECARD</h1>
            </div>

            <div class="mobbtn">
                <h1 :class="{ light: scorecard}">GEORGIA EMC SCORECARD</h1>
                <div class="btns">
                    <div class="btn list" :class="{ hovering: showList}" v-if="!scorecard" @click="listOpn">
                    </div>
                    <div class="toggle">
                        <div class="btn" :class="{ hovering: !toggleCounty}" v-if="!scorecard" @click="emcSwitch">
                            <p>EMCs</p>
                        </div>
                        <div class="btn" :class="{ hovering: toggleCounty}" v-if="!scorecard" @click="countySwitch">
                            <p>Counties</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <Buildings :scorecard = "scorecard" />
        <Houses :scorecard = "scorecard" />

        <Map @openScoreCard="scoreCard" :emcLHover="emcLHover" :emcLId="emcLId" :countyLHover="countyLHover" :countyLId="countyLId" :scorecard="scorecard" :Counties="Counties" :EMCs="EMCs" :SummaryScores="SummaryScores" :toggleCounty="toggleCounty" :showList="showList" :theCnt="theCnt" :racialRatio="racialRatio"
        :incomeRatio="incomeRatio" :EMCcounties="EMCcounties" 
        @hoverCnt="hoverCnt" v-if="!scorecard" />

        <Main :scorecard = "scorecard" :SummaryScores="SummaryScores" :emcid = "emcid" />
        <CardMain :scorecard = "scorecard" :Questions = "QandA" :emcid = "emcid" :Scores = "Scores" :SummaryScores="SummaryScores" :AllTotals="AllTotals" :EMCcounties="EMCcounties" :EmcCta="EmcCta" :QCta="QCta" @closeCard="scorecard = false" @genPDF="genPDF()" @nextEMC="nextEmc()" @prevEMC="prevEmc()" />
        <EMCList v-if="showList && !toggleCounty" 
        :EMCs = "EMCs" @data-emc="listEmc" @open-card="scoreCard" />
        <CountyList v-if="showList && toggleCounty" 
        :Counties = "Counties" @data-cnt="listCnt" @pp-cnt="openPPCnt"
        />

        <CountyPP v-if="toggleCounty && popup" :theCnt="theCnt" :racialRatio="racialRatio" :incomeRatio="incomeRatio" :SummaryScores="SummaryScores" :EMCcounties="EMCcounties"  class="popup" @closePP="popup = false" @prevCnt="prevCnt" @nextCnt="nextCnt" :headerIcon="true" @openEmc="openEmc" />

        <PDFgen v-if="PDF" :scorecard = "scorecard" :Questions = "Questions" :emcid = "emcid" :Scores = "Scores" :SummaryScores="SummaryScores" :AllTotals="AllTotals" :EMCcounties="EMCcounties" :EmcCta="EmcCta" :QCta="QCta" @closePDF = "closePDF" />
    </div>
</template>
  
<script>
import Buildings from '@/components/Bg/Buildings.vue';
import Houses from '@/components/Bg/Houses.vue';
import Map from '@/components/Map/Map.vue';
import Main from '@/components/Bg/Main.vue';
import CardMain from '../components/Card/CardMain.vue';
import EMCList from '../components/Lists/EmcList.vue';
import CountyList from '@/components/Lists/CountyList.vue';
import CountyPP from '@/components/Lists/CountyPP.vue';
import PDFgen from '@/components/PDFgen.vue';
  
  export default {
    name: "HHome",
    data() {
        return {
            scorecard: false,
            emcLHover: false,
            emcLId: '',
            countyLHover: false,
            countyLId: '',
            popup: false,
            emcid: '',
            toggleCounty: false, // = EMC is selected as default
            showList: false,
            PDF: false
        }
    },
    components: { Buildings, Houses, Map, Main, CardMain, EMCList, CountyList, CountyPP, PDFgen },
    props: {
        Scores: Array,
        Questions: Array,
        Counties: Array,
        EmcCta: Array,
        QCta: Array
    },
    methods: {
        scoreCard(v) {
            if(v) {this.emcid = v} 
            this.scorecard = !this.scorecard
            if(this.emcLHover) { this.emcLHover = false }
            if(this.countyLHover) { this.countyLHover = false }
            if(this.showList) {this.showList = false}
        },
        emcSwitch() {
            this.toggleCounty = false
            if(this.scorecard) { this.scorecard = false }
        },
        countySwitch() {
            this.toggleCounty = true
            if(this.scorecard) { this.scorecard = false }
        },
        emcScreenSwitch(){
            if(this.scorecard) { this.scorecard = false }
            if(this.toggleCounty) { this.showList = true }
            else { this.showList = !this.showList }
            this.toggleCounty = false
        },
        countyScreenSwitch(){
            if(!this.toggleCounty) { this.showList = true }
            else { this.showList = !this.showList }
            this.toggleCounty = true
        },
        listEmc(e) {
            if(e.hover) {
                this.emcLHover = true
                this.emcLId = e.id
            } else {
                this.emcLHover = false
            }
        },
        listCnt(e) {
            if(e.hover) {
                this.countyLHover = true
                this.countyLId = e.id
            } else {
                this.countyLHover = false
            }
        },
        listOpn() {
            this.showList = !this.showList
        },
        openPPCnt(v) {
                this.countyLId = v
                this.popup = true
        },
        prevCnt() {
            this.popup = false
            if(this.countyLId.length > 0) {
            let id = this.Counties.findIndex(cnt => cnt.id == this.countyLId)
            if(id == null) { 
            this.countyLID =  '' }
                else { 
                    let len = this.Counties.length
                    if(id == 0) {
                        this.countyLId =  this.Counties[len - 1].id
                    } else {
                        this.countyLId =  this.Counties[id - 1].id
                    }
                }
            } else {
                this.countyLId =  ''
            }
            this.popup = true
        },
        nextCnt() {
            this.popup = false
            if(this.countyLId.length > 0) {
            let id = this.Counties.findIndex(cnt => cnt.id == this.countyLId)
            if(id == null) { 
                this.countyLId =  '' }
                else { 
                    let len = this.Counties.length
                    if(id == (len - 1)) {
                        this.countyLId =  this.Counties[0].id
                    } else {
                        this.countyLId =  this.Counties[id + 1].id
                    }
                }
            } else {
                this.countyLId =  ''
            }
            this.popup = true
        },
        hoverCnt(v) {
            this.countyLId = v
        },
        openEmc(v) {
            this.popup = false
            this.showList = false
            this.emcid = v
            this.scoreCard()
        },
        genPDF() {
            this.scorecard = false
            this.PDF = true
        },
        closePDF() {
            this.scorecard = true
            this.PDF = false
        },
        nextEmc() {
            this.scorecard = false
            let id = this.Scores[this.nextEmcId].id
            setTimeout(()=>{
                this.scoreCard(id)
            }, 1000)
        },
        prevEmc() {
            this.scorecard = false
            let id = this.Scores[this.prevEmcId].id
            setTimeout(()=>{
                this.scoreCard(id)
            }, 1000)
        }
    },
    computed: {
        EMCcounties() {
            let allEmc = []
            this.Scores.forEach(s => {
                let array = s.counties.split(', ')
                let aEmc = []
                array.forEach(c => {
                    this.Counties.filter(co => {
                        if(co.id == c) {
                            if(co.name.includes('County')) {
                                let short = co.name.slice(0, -7)
                                aEmc.push(short)
                            } else {
                                aEmc.push(co.name)
                            }
                        }
                    })
                })
                allEmc[s.id] = aEmc
            })
            return allEmc
        },
        CountyEmcs() {
            let allCounties = []
            this.Scores.forEach(e => {
                let array = e.counties.split(', ')
                array.forEach(c => {
                    if(!allCounties[c]) {
                       allCounties[c] = []
                    }
                    allCounties[c].push({"name": e.name, "id": e.id})
                })
            }) 
            return allCounties
        },
        SummaryScores() {
            let allSummary = []
            this.Scores.forEach((scor) => {

            let grading, styling;
            if (scor.Total < 20) { 
                grading = 'F'
                styling = 'rank-f'
            } else if (scor.Total < 40) { 
                grading = 'D'
                styling = 'rank-d'
            } else if (scor.Total < 60) { 
                grading = 'C'
                styling = 'rank-c'
            } else if (scor.Total < 80) { 
                grading = 'B'
                styling = 'rank-b'
            } else { 
                grading = 'A'
                styling = 'rank-a'
            }

            let dgStyle, fcStyle, psStyle;

            if (scor.dgTotal < 8.8) { dgStyle = 'rank-f' }
            else if (scor.dgTotal < 17.6) { dgStyle = 'rank-d' }
            else if (scor.dgTotal < 26.4) { dgStyle = 'rank-c' }
            else if (scor.dgTotal < 35.2) { dgStyle = 'rank-b' }
            else { dgStyle = 'rank-a' }

            if (scor.fcTotal < 4) { fcStyle = 'rank-f' }
            else if (scor.fcTotal < 8) { fcStyle = 'rank-d' }
            else if (scor.fcTotal < 12) { fcStyle = 'rank-c' }
            else if (scor.fcTotal < 16) { fcStyle = 'rank-b' }
            else { fcStyle = 'rank-a' }

            if (scor.psTotal < 7.2) { psStyle = 'rank-f' }
            else if (scor.psTotal < 14.4) { psStyle = 'rank-d' }
            else if (scor.psTotal < 21.6) { psStyle = 'rank-c' }
            else if (scor.psTotal < 28.8) { psStyle = 'rank-b' }
            else { psStyle = 'rank-a' }
            
            let aSummary = { "id" : scor.id, "total" : scor.Total, "grade" : grading, "dg" : scor.dgTotal, "fc" : scor.fcTotal, "ps" : scor.psTotal, "style" : styling, "dgStyle": dgStyle, "fcStyle": fcStyle, "psStyle": psStyle, "customers": scor.customers, "counties": this.EMCcounties[scor.id] }

            allSummary.push(aSummary)
            })
            return allSummary
        },
        AllTotals() {
            let scor = this.SummaryScores
            let sc = scor.sort((a,b) => {
                let fa = a.total, fb = b.total
                if(fa>fb) {return -1}
                if(fb>fa) {return 1}
                return 0
            })
            let labels = []
            let points = []
            let colors = []
            sc.forEach(sco => {
                let tot = sco.total
                let id = sco.id
                let col
                if (sco.total < 20) { 
                    col = '#FF3939'
                } else if (sco.total < 40) { 
                    col = '#FF9100'
                } else if (sco.total < 60) { 
                    col = '#fffb00'
                } else if (sco.total < 80) { 
                    col = '#A8FF00'
                } else { 
                    col = '#03E538'
                }
                labels.push(id)
                points.push(tot)
                colors.push(col)
            })
            let data = { 
                labels: labels,
                datasets: [
                {   data: points,
                    backgroundColor: colors
            }]}
            return data
        },
        EMCs() {
            let EMCList = []
            this.Scores.forEach(e => {
                let data = { id: e.id, name: e.name }
                EMCList.push(data)
            })
            return EMCList
        },
        theCnt() {
            if(this.countyLId.length > 0) {
            let result = this.Counties.filter(cnt => cnt.id == this.countyLId)
            if(result.length == 0) { 
                return {"id": ''} }
            else { 
                if(this.CountyEmcs[this.countyLId]) {
                    result[0]["emcs"] = this.CountyEmcs[this.countyLId]
                } else {
                    result[0]["emcs"] = []
                }
                return result[0] 
            }
            } else {
                return {"id": ''}
            }
        },
        racialRatio() {
        if(this.countyLId.length > 0) {
        let wh = (this.theCnt.white * 100).toFixed(1);
        let bl = (this.theCnt.black * 100).toFixed(1);
        let ot = (100 - wh - bl).toFixed(1);
        let maj = wh > bl ? 'White' : 'Black'
        let data = []
        data[0] = maj 
        data[1] = [
            { label : "White", value : Number(wh), color:"#dddddd" },
            { label : "Black", value : Number(bl), color:"#111111" },
            { label : "Other", value : Number(ot), color:"#888888" }
        ]
        return data
        } else {
            return [{ Majority: '', num:[ { label : "White", value : 30, color:"#dddddd" },
            { label : "Black", value : 30, color:"#111111" },
            { label : "Other", value : 30, color:"#888888" }] }]
        }
        },
        incomeRatio() {
        if(this.countyLId.length > 0) {
        let str = this.theCnt.incomestress
        let level;
        if(str < 1.8) {level = 'High'} 
        else if(str > 3) {level = 'Low'}
        else {level = 'Average'}
        let inc = this.theCnt.income
        let pp, hoh
        if(this.theCnt.population.includes(',')) {
            pp = this.theCnt.population.replaceAll(',', '')
        } else {
            pp = this.theCnt.population
        }
        if(this.theCnt.numofhouseholds.includes(',')) {
            hoh = this.theCnt.numofhouseholds.replaceAll(',', '')
        } else {
            hoh = this.theCnt.numofhouseholds
        }
        let hhmember = pp / hoh
        let hh = hhmember.toFixed(2)
        let data = { "Level": level, "Income": inc, "HHMember": hh }
        return data
        } else {
            return { "Level": '', "Income": '', "HHMember": '' }
        }
        },
        nextEmcId() {
            let index = null
            if (this.emcid !== '') {
            this.Scores.forEach((e, i) => { 
                if(e.id == this.emcid)
                index = i
             })
            }
            let nextNum
            if(index == (this.Scores.length - 1)) { nextNum = 0 } 
            else { nextNum = index + 1 }
            return nextNum
        },
        prevEmcId() {
            let index = null
            if (this.emcid !== '') {
            this.Scores.forEach((e, i) => { 
                if(e.id == this.emcid)
                index = i
             })
            }
            let prevNum
            if(index == 0) { prevNum = this.Scores.length - 1 } 
            else { prevNum = index - 1 }
            return prevNum
        },
        QandA() {
            let q = this.Questions
            let sc = this.Scores
            q.forEach((qa) => {
                let scsys = qa.scoresys
                let le = scsys.length
                if(le > 0) { // Count each answers for the donut chart
                    sc.forEach(s => {
                        for(let v=0; v<le; v++) {
                            if(!scsys[v]["totals"]) {
                                scsys[v]["totals"] = 0
                            }                        if(scsys[v].score == s[qa.id]) {
                                scsys[v]["totals"]++
                            }
                        }
                    })
                } else if (qa.id == "fc-02" || qa.id == "fc-03") {
                    let all = []
                    sc.forEach(s => {
                        let val = { "id": s.id, "answer": s[qa.id] }
                        all.push(val)
                    })
                    all.sort((a,b) => {
                        let fa = a.answer, fb = b.answer
                        if(fa>fb) {return -1}
                        if(fb>fa) {return 1}
                        return 0
                    })
                    qa["totals"] = all
                } else { // Make an array of all answers for the bar chart
                    let all = []
                    sc.forEach(s => {
                        let val = { "id": s.id, "answer": s[qa.id] }
                        all.push(val)
                    })
                    all.sort((a,b) => {
                        let fa = a.answer, fb = b.answer
                        if(fa<fb) {return -1}
                        if(fb<fa) {return 1}
                        return 0
                    })
                    qa["totals"] = all
                }
            })
            return q
        }
    }
}
  </script>
  
  <style scoped>
    .home {
        width: 100%;
        height: 100%;
        margin: 0; padding: 0;
        top: 0; left: 0;
        background: #fefefe;
        position: absolute;
    }
    .home.pdf {
        height: fit-content;
    }
    .sky {
        background: #171752;
        transition-duration: 1000ms;
    }
    .header {
        display: flex;
        top: 10px; left: 0;
        height: 40px;
        width: calc(100% - 40px);
        /* background: #111; */
        padding: 5px 20px;
        margin: 0;
    }
    .header h1 {
        color: #AAA;
        text-align: center;
        position: fixed;
        top: 10px;
        left: 50%;
        transform: translateX(-50%);
        font-size: 1.2em;
    }
    .header h1.light {
        color: #AAAAAA;
    }
    .lists, .menus {
        top: 15px;
        display: flex;
        position: fixed;
        z-index: 5000;
    }
    .lists { left: 20px; }
    .menus { right: 20px; }
    .btn {
        width: 120px;
        display: flex;
        height: 20px;
        padding: 5px 10px;
        margin: 5px;
        gap: 10px;
        background: #231F20;
        border-radius: 5px;
        justify-content: center;
        color: #AAA;
        border: #231F20 solid 1px;
        position: relative;
        cursor: pointer;
    }
    .btn.list::after {
        content: "";
        width: 20px;
        height: 20px;
        position: absolute;
        left: 20px;
        top: 5px;
        background: url('../assets/images/card/list.svg');
    }
    .btn.list.hovering:after, .btn.hovering:after {
        background: url('../assets/images/card/list-dark.svg');
    }
    
    .btn p {
        display: block;
        height: 20px;
        margin: 0;
    }
    .btn.list p {
        margin: 0 0 0 20px;
    }
    .btn.list.hovering, .btn.hovering {
        background: #d3413f;
        color: #eee;
        transition-duration: 200ms;
        border: #d3413f solid 1px;

    }

    @media (hover: hover) {
        .btn:hover {
            background: #d3413f;
        color: #eee;
        transition-duration: 200ms;
        }
        .btn.list:hover:after {
            background: url('../assets/images/card/list-dark.svg');
        }
        .btn:hover {
        background: #d3413f;
        color: #eee;
        transition-duration: 200ms;
        border: #d3413f solid 1px;

    }
    }

    @media (hover: none) {
        .btn:hover {
            background: #231F20;
            color: #AAA;
        }
        .btn.list:hover:after {
            background: url('../assets/images/card/list.svg');
        }
        .btn.list.hovering:after {
            background: url('../assets/images/card/list-dark.svg');
        }
        .btn.list.hovering, .btn.hovering {
            background: #d3413f;
            color: #eee;
            transition-duration: 200ms;
        }
    }
    .popup {
        width: 400px;
        max-width: calc(100% - 20px);
        padding: 0 10px;
        height: calc(100% - 60px);
        background: #FFF;
        position: fixed;
        top: 60px;
        left: 0;
        border-top: #231F20 1px solid;
        border-right: #231F20 1px solid;
        overflow-y: auto;
        z-index: 2000;
    }
    @media only screen and (max-width: 780px) {
        .popup {
        width: calc(100% - 20px);
        border-right: none;
        }
    }
    .closebtn {
        width: 40px;
        height: 40px;
        border-radius: 5px;
        cursor: pointer;
        position: absolute;
        background-image: url('../assets/images/card/closebtn.svg');
        right: 10px;
    }
    .mobbtn {
            display: none;
            z-index: 1000;
            height: fit-content;
        }

    
    .btn.list {
        width:40px;
        margin: 5px 10px 5px 0;
        flex-shrink: 0;
    }
    .toggle {
        display: flex;
    }
    .toggle .btn {
        margin: 5px 0;
        width: 90px;
    }
    .toggle.zoom {
        margin: 0 0 0 10px;
    }
    .toggle.zoom .btn {
        width: 20px;
    }
    .toggle .btn:nth-of-type(1) {
        border-radius: 5px 0 0 5px;
    }
    .toggle .btn:nth-of-type(2) {
        border-radius: 0 5px 5px 0;
    }

    @media only screen and (max-width: 900px) {
        .lists {
            display: none;
            /* width: 100%;
            left: -80px; */
        }
        .mobbtn {
            display: block;
            width: 100%;
            text-align: center;
        }
        .mobbtn h1 {
            /* position: fixed; */
            width: 100%;
            /* top: 0; */
        }
        .btns {
            display: flex;
            justify-content: center;
            margin: 50px auto 0 auto;
        }
        /* .btn.list {
            top: 30px;
            left: 50%;
            transform: translate(-50%, 0);
        } */
    }

  </style>