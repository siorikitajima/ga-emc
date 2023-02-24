<template>
    <div>
        <div class="list">
            <div v-for="c in Counties" :key="c.id" :data-cnt="c.id"
            @mouseover="hoverCnt" 
            @mouseleave="unhoverCnt"
            @click="openPPCnt">
            {{c.name}}</div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'EMCList',
        data() {
            return {
                hover: false
            }
        },
        props: {
                Counties: Array 
            },
        methods: {
            hoverCnt(e) {
                let id = e.target.getAttribute('data-cnt')
                this.$emit('data-cnt', {
                    'id': id,
                    'hover': true
                })
            },
            unhoverCnt(e) {
                let id = e.target.getAttribute('data-cnt')
                this.$emit('data-cnt', {
                    'id': id,
                    'hover': false
                })
            },
            openPPCnt(e) {
                if(this.$isMobile()) {
                    let id = e.target.getAttribute('data-cnt')
                    this.$emit('pp-cnt', id)
                }
            },
            openCCEmc(e) {
                this.hover = false
                let id = e.target.getAttribute
                this.$emit('openEmc', id)
            }
        }
    }
</script>

<style scoped>
.list {
    position: fixed;
    width: 200px;
    height: calc(100% - 70px);
    overflow-y: auto;
    top: 60px;
    left: 25px;
    background: #111;
    color: #DDD;
}
.list div {
    padding: 5px 10px;
}
.list div:nth-of-type(2n) {
    background: #444;
}
@media (hover: hover) {
    .list div:hover {
        background: #d3413f;
        color: #eee;
    }
}
@media only screen and (max-width: 780px) {
    .list {
        top: 100px;
        width: 100%;
        left: 0;
        height: calc(100% - 100px);
    }
    .list div {
        padding: 10px;
    }
}
</style>