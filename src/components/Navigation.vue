<template>
  <ul>
    <li><label id="title">チャットルーム</label></li>
    <div id="button">
        <li v-if="user != null">
        <img :src="user.photoURL" style="width:40px; float:left; margin-right:1em" />
      {{user.displayName}}
      <button style="font-size: 0.8em" @click="$emit('logoutButtonClicked')">ログアウト</button>
    </li>
    <li v-else>
      {{user}}
      <button style="font-size: 0.8em" @click="$emit('loginButtonClicked')">ログイン</button>
    </li>
    </div>
  </ul>
</template>

<script lang="ts">
import { PropType } from 'vue'
import firebase from 'firebase'
export default {
  name: 'Navigation',
  props: {
    user: Object as PropType<firebase.User>
  },
  emits: ['loginButtonClicked', 'logoutButtonClicked']
}
</script>

<style scoped>
ul {
    width: 100%;
    list-style-type: none;
    border-bottom:lightgray 1px solid;
    display: flex;
    justify-content: space-evenly;
}

#title {
  font-size: 2.5em;
}

#button {
  margin-top:10px;
  font-size: 1.5em;
}
</style>