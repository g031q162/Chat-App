<template>
<div class="content" id="messages-box">
  <ul id = "app">
      <li v-for="chat in chats" :key="chat.msg">
        <p style=”display:inline”>{{chat.name}}：</p>
        <p style=”display:inline”>{{chat.msg}}</p> 
        <p style="font-size: 80%; margin-top:30px;">&nbsp;({{chat.timestamp}})</p>
      </li>
  </ul>
</div>
<ul>
      <li >
          <div class="send-area">
              <input id="message" type="text" v-model="msg2.msg"/>
              <button id="send" class="button" @click="$emit('sendButtonClicked', msg2)">送信</button>                
          </div>
      </li>
  </ul>
</template>

<script lang="ts">  
export interface Chat {
  id: Number,
  msg: string,
  name: string,
  timestamp: string
}
import { PropType } from 'vue'
import firebase from 'firebase/app'
import 'firebase/firestore'
export default {
  name: "ChatScreen",
  props: {
    msg2: Object as PropType<Chat>,
    user: Object as PropType<firebase.User>
  },
  emits: ['sendButtonClicked'],
  el: '#app',
  data(){
    return{
      chats: [],
      msg2: [],
    };
  },
  mounted() {
    let db = this.db as firebase.firestore.Firestore;
    db.collection('data/messageData/message').onSnapshot(() => {
      this.getChat();
    });
  },
  methods: {
    getChat(){
      let db = this.db as firebase.firestore.Firestore;
    db.collection('data/messageData/message').orderBy('id', 'asc').get().then((querySnapshot) => {
      let chats = [];

      querySnapshot.forEach((doc) => {
        chats.push(doc.data());
      });

      this.chats = chats;

      this.msg2.msg = '';

      let obj = document.getElementById('messages-box');
          obj.scrollTop = obj.scrollHeight;
    })
    .catch(error => {
      console.log(error);
    });
    }
  }
}

</script>

<style scoped>
ul {
    list-style-type: none;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 1em;
}
ul li  {
    display: inline-flex;
    width: 100%;
    margin: 0em;
    padding: 0em;
}

.content {
    overflow-y: scroll;
    width: 100%;
    position: fixed;
    top:120px;
    bottom: 110px;
    font-size: 1.5em;
    padding: 0 8%;
}

.content #other{
    padding: 0 8%;
    text-align: left;
}

.content #my{
    padding: 0 8%;
    text-align: right;
}

.send-area {
  padding: 0 0px;
  box-sizing: border-box;
  line-height: 70px;
  position: fixed;
  bottom: 30px;
  width: 100%;
  border-top: solid 2px black;
}

.send-area #message{
    display: inline-block;
    position: relative;
    height: 1.5em;
    font-size: 1.5em;
    width: calc(100% - 20%);
}

.send-area input {
  vertical-align: middle;
  position: relative;
  width: 100%;
}

.send-area #send {
  height: 1.8em;
  font-size: 1.5em;
  vertical-align: middle;
}
</style>