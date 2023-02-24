<template>
    <div class="footer" v-if="ThisQCta">
    <h3 style="text-align:center;font-family:'Open Sans',Helvetica;">// Call to Action //</h3>
    <p style="text-align:center;font-family:'Open Sans',Helvetica;"><i>Suggestions for the EMC to improve the score.</i></p>
    <ul v-if="ThisEmcCta[0]">
        <li style="font-family:'Open Sans',Helvetica;">
            <img src="@/assets/images/card/check.png"/>{{ThisEmcCta[0]['cta1']}}
        </li>
        <li style="font-family:'Open Sans',Helvetica;">
            <img src="@/assets/images/card/check.png"/>{{ThisEmcCta[0]['cta2']}}
        </li>
        <li style="font-family:'Open Sans',Helvetica;">
            <img src="@/assets/images/card/check.png"/>{{ThisEmcCta[0]['cta3']}}
        </li>
    </ul>
    <ul v-else>
        <li v-for="cta in ThisQCta" :key="cta.order" style="font-family:'Open Sans',Helvetica;">
            <img src="@/assets/images/card/check.png"/>{{cta.cta}}
        </li>
    </ul>
    <p  style="text-align:center; font-family:'Open Sans',Helvetica;"><i>If your EMC's data is inaccurate or incomplete, please be in touch with us by <a id="mailLink" target="_blank" :href="mailto">email</a> so we can make the necessary change.</i></p>
</div>
</template>

<script>
export default {
    name: "CardFooter",
    computed: {
        mailto() { // Prevent bots to read it as an email address
            return 'mai' + 'lto:i' + 'nf' + 'o' + '@' + 'gcvot' + 'ers.o' + 'rg'
        }
    },
    props: {
        ThisEmcCta: Array,
        ThisQCta: Array
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
.footer {
        width: calc(100% - 80px);
        padding: 10px 40px;
        background: #231F20;
        color: #DDD;
        font-size: 14px;
        text-align: left;
    }
    .footer h3 {
        margin: 20px 0 0 0;
    }
    .footer ul {
        list-style: none;
        padding: 0;
        margin: 0;
    }
    .footer ul li {
        display: block;
        margin: 5px auto;
        padding: 10px 0 ;
        border-bottom: #AAA solid 2px;
        break-inside: avoid;
    }
    .footer ul li img {
        width: 18px;
        margin: auto 10px -2px auto;
    }
</style>