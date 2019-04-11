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
        <li>
          <button type="button" class="btn btn-default" @click="toggleStatus(item)">
            {{String(item.status)}}
          </button>
          {{item.name}}
        </li>
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
    });
    this.itemsRef = firebase.database().ref('items');
    const self = this;
    this.itemsRef.on('value', function(snapshot) {
       self.list = snapshot.val();
    });
  },

  methods: {
    // ログイン関数
    login () {
      firebase.auth().signInAnonymously().then(e => {
        // eslint-disable-next-line
        console.log(e)
      }).catch((error) => {
        // ログインのエラーメッセージ
        const errorMessage = error.message;
        const errorCode = error.code;
        // eslint-disable-next-line
        console.log('ログインエラーメッセージ', errorCode, errorMessage)
      });
    },
    // ダミーデータをfirebaseに送信
    pushData () {
      this.itemsRef.push({
        name: 'hoge',
        status: false
      })
    },

    toggleStatus (item) {
      firebase.database().ref('items/' + item.name).update({
        name: item.name,
        status: !item.status
      })
    }
  },

  data() {
    return {
      list: []
    }
  }
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
