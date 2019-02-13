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
    this.boardRef = firebase.database().ref('myBoard');
    let self = this;
    this.boardRef.on('value', function(snapshot) {
       self.list = snapshot.val();
    });
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
    // ダミーデータをfirebaseに送信
    pushData () {
      this.boardRef.push({
          name: 'test',
          message: 'hoge'
      })
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
