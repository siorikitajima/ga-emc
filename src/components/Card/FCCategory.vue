<template>
  <div class="ctg" :class="{pdf: pdf}" ref="header">
    <h3 style="text-align: center; font-family:'Open Sans', Helvetica;">// {{ ctgcontent }} //</h3>
    <!-- for PDF -->
    <div class="subctg" v-if="pdf">
        <div class="tablehead pdf">
            <div class="q"  style="font-family:'Open Sans', Helvetica;">Member-Owner Standards</div>
            <div class="a"  style="font-family:'Open Sans', Helvetica;">Comparison</div>
        </div>
        <div class="wrapper">
            <NestedQuestion :close="close" :question="reorderedQ00" :scr="ScrFc01" :emcid="emcid" :scoresys="reorderedQ00.scoresys" :pdf="pdf" :SummaryScore="SummaryScore" :nestedScr="nestedScr"/>
        </div>
        <div class="wrapper">
            <Question :close="close" :question="reorderedQ01" :scr="ScrFc12" :emcid="emcid" :scoresys="reorderedQ01.scoresys" :pdf="pdf" :SummaryScore="SummaryScore"/>
        </div>
        <div class="wrapper">
            <NestedQuestion :close="close" :question="reorderedQ02" :scr="ScrFc05" :emcid="emcid" :scoresys="reorderedQ02.scoresys" :pdf="pdf" :SummaryScore="SummaryScore" :nestedScr="nestedScr"/>
        </div>
        <div class="wrapper">
            <NestedQuestion :close="close" :question="reorderedQ03" :scr="ScrFc10" :emcid="emcid" :scoresys="reorderedQ03.scoresys" :pdf="pdf" :SummaryScore="SummaryScore" :nestedScr="nestedScr" :nestedFc10Q="reorderedQ04"/>
        </div>
    </div>
    <!-- for Screen -->
    <div class="subctg" v-else>
        <div class="tablehead">
            <div class="q">Member-Owner Standards</div>
            <div class="s">Score</div>
        </div>

        <div class="wrapper">
            <NestedQuestion :close="close" :question="reorderedQ00" :scr="ScrFc01" :emcid="emcid" :scoresys="reorderedQ00.scoresys" :pdf="pdf" :SummaryScore="SummaryScore" :nestedScr="nestedScr"/>
        </div>
        <div class="wrapper">
            <Question :close="close" :question="reorderedQ01" :scr="ScrFc12" :emcid="emcid" :scoresys="reorderedQ01.scoresys" :pdf="pdf" :SummaryScore="SummaryScore"/>
        </div>
        <div class="wrapper">
            <NestedQuestion :close="close" :question="reorderedQ02" :scr="ScrFc05" :emcid="emcid" :scoresys="reorderedQ02.scoresys" :pdf="pdf" :SummaryScore="SummaryScore" :nestedScr="nestedScr"/>
        </div>
        <div class="wrapper">
            <NestedQuestion :close="close" :question="reorderedQ03" :scr="ScrFc10" :emcid="emcid" :scoresys="reorderedQ03.scoresys" :pdf="pdf" :SummaryScore="SummaryScore" :nestedScr="nestedScr" :nestedFc10Q="reorderedQ04"/>
        </div>
    </div>
  </div>
</template>

<script>
import Question from "./Question.vue";
import NestedQuestion from "./NestedQuestion.vue";

export default {
    name: "CardFCCategory",
    props: {
        ctgcontent: String,
        questions: Array,
        scrs: Array,
        pdf: Boolean,
        SummaryScore: Object,
        emcid: String,
        close: Boolean
    },
    components: { Question, NestedQuestion },
    computed: {
        ScrFc01(){
            let thisScrs = this.scrs
            return thisScrs[0]['fc-01']
        },
        ScrFc05(){
            let thisScrs = this.scrs
            return thisScrs[0]['fc-05']
        },
        ScrFc10(){
            let thisScrs = this.scrs
            return thisScrs[0]['fc-10']
        },
        ScrFc12(){
            let thisScrs = this.scrs
            return thisScrs[0]['fc-12']
        },
        reorderedQ00() {
            if(this.emcid !== '') {
            let reQs = this.questions
                let item = reQs.filter(q => {
                    return 'fc-01' == q.id
                })
                return item[0]
            } else {
                return {"scoresys":[]}
            }
        },
        reorderedQ01() {
            if(this.emcid !== '') {
            let reQs = this.questions
                let item = reQs.filter(q => {
                    return 'fc-12' == q.id
                })
                return item[0]
            } else {
                return {"scoresys":[]}
            }
        },
        reorderedQ02() {
            if(this.emcid !== '') {
            let reQs = this.questions
                let item = reQs.filter(q => {
                    return 'fc-05' == q.id
                })

                let nest3 = ['fc-04', 'fc-02', 'fc-03']
                let nest3Q = []
                nest3.forEach(n => {
                    let item = reQs.filter(q => {
                        return n == q.id
                    })
                    nest3Q.push(item[0])
                })
                item[0]['nest'] = nest3Q

                return item[0]
            } else {
                return {"scoresys":[]}
            }
        },
        reorderedQ03() {
            if(this.emcid !== '') {
            let reQs = this.questions
                let item = reQs.filter(q => {
                    return 'fc-10' == q.id
                })

                let nest4 = ['fc-09', 'fc-02']
                let nest4Q = []                
                nest4.forEach(n => {
                    let item = reQs.filter(q => {
                        return n == q.id
                    })
                        nest4Q.push(item[0])
                })
                item[0]['nest'] = nest4Q

                return item[0]
            } else {
                return {"scoresys":[]}
            }
        },
        reorderedQ04() {
            if(this.emcid !== '') {
            let reQs = this.questions
                let nest5 = ['fc-07', 'fc-08']
                let nest5Q = []
                nest5.forEach(n => {
                    let item = reQs.filter(q => {
                        return n == q.id
                    })
                        nest5Q.push(item[0])
                })

                return nest5Q
            } else {
                return []
            }
            
        },
        nestedScr() {
            if(this.emcid !== ''){
                let thisScrs = this.scrs
                let val = thisScrs.filter(s => { return s.id == this.emcid})
                return val[0]
            } else {
                return {}
            }
        }
    }
}
</script>

<style scoped>
    .ctg {
        width: calc(100% - 40px);
        margin: 20px auto;
    }
    .flex {
        display: grid;
        grid-template-columns: calc(50% - 5px) calc(50% - 5px);
        width: 100%;
        gap: 10px;
    }
    .ctg h3 {
        margin: 30px auto 10px auto;
        color: #aaa;
    }
    .ctg.pdf h3 {
        margin: 10px auto;
    }
    .tablehead {
        width: 100%;
        background: #CCC;
        color: #666;
        display: flex;
        justify-content: space-between;
        gap: 3px;
        margin-bottom: 5px;
        font-size: 0.8em;
    }
    .tablehead div {
        padding: 3px 10px;
    }
    .tablehead div.s {
        width: 400px;
        padding-right: 60px;
        text-align: right;
    }
    .r {
        width: 60px;
    }
    .q {
        width: inherit;
        text-align: left;
    }
    .pdf .s {
        width: 100px;
    }
    .pdf .r {
        width: 100px;
    }
    .pdf .q {
        width: 370px;
        text-align: left;
    }
    .pdf .a {
        width: 290px;
        text-align: right;
    }

</style>
