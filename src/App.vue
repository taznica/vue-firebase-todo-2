<template>
  <div id="app">
    <div>
      <button type="button" class="btn btn-default" @click="login">
        匿名ユーザーでログイン
      </button>
      <button type="button" class="btn btn-default" @click="pushData">
        送信
      </button>
    </div>
    <div>
      <ul v-for="item in list" :key="item.name">
        <li>{{item.name}} / {{item.message}}</li>
      </ul>
    </div>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import firebase from 'firebase'

export default {
  name: 'app',
  components: {},


  created() {
    firebase.auth().onAuthStateChanged( user => {
      if (user) {
        // User is signed in.
        // eslint-disable-next-line
        console.log('is login.')
      } else {
        // No user is signed in.
        // eslint-disable-next-line
        console.log('No user is signed in.')
      }
    })
    this.listen()
  },

  methods: {
    // ログイン関数
    login () {
      firebase.auth().signInAnonymously().then(e => {
        // eslint-disable-next-line
        console.log(e)
        this.listen()
      }).catch((error) => {
        // ログインのエラーメッセージ
        var errorCode = error.code;
        var errorMessage = error.message;
        // eslint-disable-next-line
        console.log('ログインエラーメッセージ', errorCode, errorMessage)
      });
    },
    // データベースの変更を購読、最新状態をlistにコピーする
    listen () {
      firebase.database().ref('myBoard/').on('value', snapshot => {
        if (snapshot) {
          const rootList = snapshot.val()
          let list = []
          Object.keys(rootList).forEach((val, key) => {
            rootList[val].id = val
            list.push(rootList[val])
          })
          this.list = list
        }
      })
    },
    // ダミーデータをfirebaseに送信
    pushData () {
      firebase.database().ref('myBoard/').push({
        name: 'test',
        message: 'hoge'
      }).then(this.listen())
    },
  },

  data() {
    return {
      list: []
    }
  },
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
