<template>
    <div class="list">
        <div v-for="emc in EMCs" :key="emc.id" :data-emc="emc.id" 
        @mouseover="hoverEmc" 
        @mouseleave="unhoverEmc"
        @click="clickLEmc"
        >
        {{emc.name}}</div>
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
                EMCs: Array 
            },
        methods: {
            hoverEmc(e) {
                let id = e.target.getAttribute('data-emc')
                this.$emit('data-emc', {
                    'id': id,
                    'hover': true
                })
            },
            unhoverEmc(e) {
                let id = e.target.getAttribute('data-emc')
                this.$emit('data-emc', {
                    'id': id,
                    'hover': false
                })
            },
            clickLEmc(e) {
                let id = e.target.getAttribute('data-emc')
                this.$emit('open-card', id)
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