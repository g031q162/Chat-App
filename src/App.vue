<template>
  <navigation 
  :user="user"
  @loginButtonClicked="login"
  @logoutButtonClicked="logout"
  />
  <chat-screen 
  :msg2="currentMsg"
  @sendButtonClicked="sendMessage"
  />
</template>

<script lang="ts">
import ChatScreen, {Chat} from './components/ChatScreen.vue'
import Navigation from './components/Navigation.vue'
import { reactive } from 'vue'
import firebase from 'firebase/app'
import 'firebase/firestore'
export default {
  name: 'App',
  components: {
    Navigation,
    ChatScreen
  },
  data() {
    let currentMsg = {id: -1, msg: '', name: '', timestamp: ''};
    let messages = reactive([] as unknown as [Chat]);
    let MaxMessageID = 0;
    let auth = this.auth as unknown as firebase.auth.Auth;
    let user = auth.currentUser;
    return { currentMsg, messages, MaxMessageID, user };
  },
  methods: {
    logout() {
      this.auth.signOut().then(() => {
        // thenメソッドはひとつ前の処理を非同期処理し、
        // その結果正しく処理された場合の処理を第1引数に記述する
        this.user = null;
        this.userDataPath = 'data/messageData';
      })
    },
    login() {
      const provider = new firebase.auth.GoogleAuthProvider();
      this.auth.signInWithPopup(provider).then((result) => {
        // ログインに成功した場合
        console.log('認証に成功しました。', result);
        this.user = result.user;
        this.userDataPath = `users/${this.user.uid}`;
      }).catch((error) => {
        alert('エラーが発生しました。' + error.message);
      });
    },
    sendMessage(msg3) {
      let db = this.db as firebase.firestore.Firestore;
      let messageRef = db.collection('data/messageData/message');
      let MaxMessageID;
      var date = new Date();
      let time = '';

      time = date.getFullYear() + "/" + (date.getMonth() + 1) + "/" + date.getDate() + " " + date.getHours() + ":"+ date.getMinutes();

      if (msg3['msg'] === undefined || msg3['msg'] === ''){
        alert('メッセージを入力してください');
        return;
      }

      if (this.auth.currentUser == null) {
        alert('ログインしてください');
        return;
      }

      db.doc('data/messageData').get().then((doc) => {
        
        MaxMessageID = doc.data().MaxMessageID + 1;

        messageRef.add({
        id: MaxMessageID,
        msg: msg3['msg'],
        name: this.auth.currentUser.displayName,
        timestamp: time
      });   

        doc.ref.update('MaxMessageID', MaxMessageID).then(() => {
          console.log(`update => ${MaxMessageID}`);
      });
      });
      this.currentMsg = [];
      return;
    } 
    }
  }
</script>