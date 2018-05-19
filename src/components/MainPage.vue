<template>
  <div class="main-page">
    <h1>☆11ハード難易度表</h1>
    <ul
      v-for="difficulty in difficulties"
      :key="difficulty"
    >
      <li class="header">
        {{ difficulty }}
      </li>
      <li v-for="item in by_difficulty[difficulty]"
        class="item"
        :key="item.id"
        :class="[hard_status[item.id] ? 'hard' : 'non-hard']"
        @click="itemClicked(item)"
      >
        <p>{{ item.name }}</p>
      </li>
    </ul>
  </div>
</template>

<script>

export default {
  name: 'MainPage',
  data () {
    return {
      master_data: null,
      by_difficulty: {},
      difficulties: [
        '地力S+',
        '個人差S+',
        '地力S',
        '個人差S',
        '地力A+',
        '個人差A+',
        '地力A',
        '個人差A',
        '地力B',
        '地力C',
        '地力D',
        '地力E',
        '地力F',
        '未定'
      ],
      hard_status: {}
    }
  },
  mounted () {
    var req = new XMLHttpRequest()
    req.open('get', 'master_data.csv', true)
    req.send(null)
    req.onload = () => {
      const neatCsv = require('neat-csv')
      neatCsv(req.responseText).then((data) => {
        this.master_data = data
        /*
        data = [
          {
            difficulty: '未定',
            id: '0',
            max_bpm: '178',
            min_bpm: '178',
            name: '炸裂！イェーガー電光チョップ！！(JAEGER FINAL ATTACK)(A)',
            notes: '1344',
            textage_1P: 'http://textage.cc/score/25/citynvrs.html?1AB00',
            textage_2P: 'http://textage.cc/score/25/citynvrs.html?2AB00',
            version: '25'
          }
        ]*/
        
        for (var d of data) {
          if (!this.by_difficulty[d.difficulty]) {
            this.$set(this.by_difficulty, d.difficulty, [])
          }
          this.by_difficulty[d.difficulty].push(d)
          let hard_flag = localStorage.getItem(d.id)
          if (hard_flag != null) {
            this.$set(this.hard_status, d.id, hard_flag === 'true')
          }
        }
      })
    }
  },
  methods: {
    itemClicked (item) {
      if (!(item.id in this.hard_status)) {
        this.$set(this.hard_status, item.id, false)
      }
      this.hard_status[item.id] = !this.hard_status[item.id]
      localStorage.setItem(item.id, this.hard_status[item.id])
    }
  }
}
</script>

<style scoped>
  .main-page {
    margin-bottom: 100px;
  }
  ul {
    margin: auto;
    list-style-type: none;
    padding: 0;
    width: 90%;
    text-align: center;
  }
  .header {
    text-align: center;
    font-weight: bold;
    background-color: rgb(245, 222, 179);
    margin: 5px auto 0 auto;
  }
  .item {
    display: inline-block;
    text-align: center;
    width: 200px;
    height: 70px;
    vertical-align: middle;
    padding: 5px;
  }
  .hard {
    background-color: rgb(255, 99, 71);
  }
  .non-hard {
    background-color: #f8f8f8;
  }
  p {
    margin: auto;
  }
</style>
