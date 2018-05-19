<template>
    <div class="main-page">
        <h1>☆11ハード難易度表</h1>
        <ul>
            <li class="header">地力S+</li>
            <li class="item">
                <p>Critical Crystal(A)</p>
            </li>
            <li class="item"><p>four pieces of heaven(A)</p></li>
            <li class="item">SCHWARZSCHILD FIELD(A)</li>
            <li class="item">UMMU(A)</li>
            <li class="item">V2(A)</li>
        </ul>
    </div>
</template>

<script>

export default {
    name: 'MainPage',
    data () {
        return {
            master_data: null
        }
    },
    mounted () {
        var req = new XMLHttpRequest()
        req.open('get', './assets/master_data.csv', true)
        req.send(null)
        req.onload = () => {
            const neatCsv = require('neat-csv')
            neatCsv(req.responseText).then((data) => {
                this.master_data = data
            })
        }
    }
}
</script>

<style scoped>
    ul {
        margin: auto;
        list-style-type: none;
        padding: 0;
        width: 90%;
        text-align: left;
    }
    .header {
        text-align: center;
        font-weight: bold;
        background-color: rgb(245, 222, 179);
    }
    .item {
        display: inline-block;
        text-align: center;
        width: 200px;
        height: 60px;
        vertical-align: middle;
        background-color: white;
    }
</style>
