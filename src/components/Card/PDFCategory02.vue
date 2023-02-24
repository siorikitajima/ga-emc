<template>
  <div class="ctg" :class="{pdf: pdf}" ref="header">
    <h3 style="text-align: center; font-family: 'Open Sans', Helvetica;">// {{ ctgcontent }} //</h3>
    <div class="subctg" v-if="pdf">
        <div class="table-head pdf">
            <div class="q"  style="font-family: 'Open Sans', Helvetica;">Member-Owner Standards</div>
            <div class="a"  style="font-family: 'Open Sans', Helvetica;">Comparison</div>
        </div>
            <div class="wrapper" v-for="item in questions.slice(8)" :key="item.id">
                <Question :close="close" :question="item" :scr="Scr(item.id)" :emcid="emcid" :scoresys="item.scoresys" :pdf="pdf" :SummaryScore="SummaryScore"
                />
            </div>
            <Footer :ThisQCta="ThisQCta" :ThisEmcCta="ThisEmcCta" id="footer" />
    </div>
  </div>
</template>

<script>
import Question from "./Question.vue";
import Footer from "./Footer.vue";

export default {
    name: "CardPDFCategory02",
    props: {
        ctgcontent: String,
        questions: Array,
        scrs: Array,
        pdf: Boolean,
        SummaryScore: Object,
        emcid: String,
        close: Boolean,
        ThisEmcCta: Array,
        ThisQCta: Array
    },
    components: { Question, Footer },
    methods: {
        Scr(id) {
            return this.scrs[0][id]
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
    .table-head {
        width: 100%;
        background: #CCC;
        color: #666;
        display: flex;
        justify-content: space-between;
        gap: 3px;
        margin-bottom: 5px;
        font-size: 0.8em;
    }
    .table-head div {
        padding: 3px 10px;
    }
    .table-head div.s {
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
