<template>
    <div>
        <div class="icons" v-if="headerIcon">
            <div class="icon leftarrow" @click="prevCnt">
            </div>
            <div class="icon rightarrow" @click="nextCnt">
            </div>
            <div class="closebtn" @click="closePP"></div>
        </div>
        <div class="icons" v-else>
            <div class="closebtn higher" @click="closePP"></div>
        </div>
        <h3>{{ theCnt.name }}</h3>
        <h4>Racial Majority: {{ racialRatio[0] }}</h4>

        <div class="flex">
            <div class="left">
                <vc-donut class="chart"
                    background="white" foreground="#CCC"
                    :size="80" unit="px" :thickness="30"
                    :sections="racialRatio[1]" :total="100"
                    :start-angle="0" :auto-adjust-text-size="false" style="margin: 0 20px;" >
                </vc-donut>
            </div>
            <div class="right">
                <p v-for="item in racialRatio[1]" :key="item.label">
                <b>{{ item.value }}</b>% {{ item.label }}</p>
            </div>
        </div>


        <h4>Income Level: {{ incomeRatio.Level }}</h4>
        <p>
        Median Household Income: <b>{{ incomeRatio.Income }}</b><br/>
        Average Household Member: <b>{{ incomeRatio.HHMember }}</b>
        </p>
        <hr/>
        <h5>Available EMCs</h5>
            <ul ref="emcboxes">
                <li v-for="(emc, index) in theCnt.emcs" :key="emc.id" class="emcBtn" @click="openEmc(emc.id)" :style="{'background': emcColors[index].col}" >{{ emc.name }}</li>
            </ul>
    </div>
</template>

<script>
import Donut from '../../../node_modules/vue-css-donut-chart/src/components/Donut.vue';

export default {
    name: "CountyPP",
    components: { 'vc-donut': Donut },
    data() { return {
        ranks: [ 
            { class: "rank-a", col: "#03E538" },
            { class: "rank-b", col: "#A8FF00" },
            { class: "rank-c", col: "#fffb00" },
            { class: "rank-d", col: "#FF9100" },
            { class: "rank-f", col: "#FF3939" }
        ]
    }
    },
    props: {
        theCnt: Object,
        racialRatio: Array,
        incomeRatio: Object,
        headerIcon: Boolean,
        SummaryScores: Array,
        CountyEmcs: Array
    },
    methods: {
        closePP() {
            this.$emit('closePP')
        },
        prevCnt() {
            this.$emit('prevCnt')
        },
        nextCnt() {
            this.$emit('nextCnt')
        },
        openEmc(v) {
            this.$emit('openEmc', v)
        }
    },
    computed: {
        emcColors() {
            let allCol = []
            let emcs = this.theCnt.emcs
            emcs.forEach((e) => {
                let sty = this.SummaryScores.filter(emc => emc.id == e.id)
                if(sty.length > 0) {
                    let cl = sty[0].style
                    let color = this.ranks.filter(r => r.class == cl)
                    let data = { class: cl, col: color[0].col}
                    allCol.push(data)
                } else {
                    allCol.push({ class: "rank-c", col: "#fffb00" })
                }
            })
            return allCol
        }
    }
}

</script>

<style scoped>
    .flex { 
        display: flex;
        gap: 10px;
        width: fit-content;
        margin: 0 auto;
    }
    .icons {
        display: flex;
    }
    .closebtn {
        width: 40px;
        height: 40px;
        border-radius: 5px;
        cursor: pointer;
        position: absolute;
        background-image: url('../../assets/images/card/closebtn.svg');
        right: 10px;
    }
    .higher.closebtn {
        right: 0;
        top: 0;
    }
    .icon {
        width: 40px;
        height: 40px;
        border-radius: 5px;
        cursor: pointer;
    }
    .leftarrow {
        background-image: url('../../assets/images/card/leftarrow.svg');
        left: 10px;
    }
    .rightarrow {
        background-image: url('../../assets/images/card/rightarrow.svg');
        left: 60px;
    }
    p {
        line-height: 1.5em;
        margin: 0 0 10px 0;
    }
    .flex .right p {
        margin: 0 0 5px 0;
    }
    ul {
        margin: 0;
        padding: 0;
    }
    ul li {
        list-style: none;
        margin: 5px 0;
        padding: 5px;
        /* background: #d3413f; */
        border-radius: 5px;
        color: #231F20;
        border: none;
        cursor: pointer;
    }
    @media (hover: hover) {
        ul li:hover {
            opacity: 0.6;
            transition-duration: 100ms;
        }
    }
    ul li.emcBtn {
        margin: 10px 0;
        padding: 10px;
    }
    h4 {
        background: #EEE;
        padding: 10px;
        color: #231F20;
        margin: 10px 0;
    }
    h3, h5 {
    margin: 0 0 5px 0;
    padding: 0;
    }
    hr { margin: 20px 0;}
</style>