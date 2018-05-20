<template>
  <div class="main-page">
    <h1>☆11ハード難易度表</h1>
    <import-modal
      v-if="showImportModal"
      @import="importData"
      @close="showImportModal = false"
    />
    <export-modal
      v-if="showExportModal"
      @close="showExportModal = false"
      :token="token"
    />
    <button @click="showImportModal = true">インポート</button>
    <button @click="showExportModal = true">エクスポート</button>
    <h4>{{hardCnt}} / {{masterData.length}} ハード済み</h4>
    <ul
      v-for="difficulty in difficulties"
      :key="difficulty"
    >
      <li class="header">
        {{ difficulty }}
      </li>
      <li v-for="item in byDifficulty[difficulty]"
        class="item"
        :key="item.id"
        :class="[hardStatus[item.id] ? 'hard' : 'non-hard']"
        @click="itemClicked(item)"
      >
        <p>{{ item.name }}</p>
      </li>
    </ul>
    <p>出典：<a href="https://www65.atwiki.jp/bemani2sp11/pages/12.html">☆11 (ハード難易度表)</a></p>
  </div>
</template>

<script>
import ImportModal from './ImportModal.vue'
import ExportModal from './ExportModal.vue'

export default {
  name: 'MainPage',
  data () {
    return {
      masterData: null,
      byDifficulty: {},
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
      hardStatus: {},
      hardCnt: 0,
      showImportModal: false,
      showExportModal: false
    }
  },
  components: {
    'import-modal': ImportModal,
    'export-modal': ExportModal
  },
  mounted () {
    var req = new XMLHttpRequest()
    req.open('get', 'master_data.csv', true)
    req.send(null)
    req.onload = () => {
      const neatCsv = require('neat-csv')
      neatCsv(req.responseText).then((data) => {
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
        ]
        */
        this.masterData = data
        for (var d of data) {
          if (!this.byDifficulty[d.difficulty]) {
            this.$set(this.byDifficulty, d.difficulty, [])
          }
          this.byDifficulty[d.difficulty].push(d)
          let hardFlag = localStorage.getItem(d.id)
          this.$set(this.hardStatus, d.id, hardFlag === 'true')
          if (hardFlag === 'true') {
            this.hardCnt += 1
          }
        }
      })
    }
  },
  methods: {
    itemClicked (item) {
      if (!(item.id in this.hardStatus)) {
        this.$set(this.hardStatus, item.id, false)
      }
      if (this.hardStatus[item.id]) {
        this.hardCnt -= 1
      } else {
        this.hardCnt += 1
      }
      this.hardStatus[item.id] = !this.hardStatus[item.id]
      localStorage.setItem(item.id, this.hardStatus[item.id])
    },
    importData (token) {
      var jwt = require('jsonwebtoken')
      try{
        this.hardStatus = jwt.verify(token, 'jwt_key')
        for (let key in this.hardStatus){
          localStorage.setItem(key, this.hardStatus[key])
        }
      } catch(err) {
        alert('文字列が正しくありません')
      }
    }
  },
  computed: {
    token: function () {
      var jwt = require('jsonwebtoken')
      return jwt.sign(this.hardStatus, 'jwt_key')
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
