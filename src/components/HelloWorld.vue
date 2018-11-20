<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div v-if="user"> {{ user.displayName }} </div>
    <button @click="signin">Sign In</button>
    <div id="input">
      <input v-model="inputMessage" @keyup.enter="send">
    </div>
    <div id="outputs">
        <div v-for="message in messages" :key="message.name">
          {{ message.username }}: {{message.content}}
        </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import firebase from 'firebase/app';
import 'firebase/auth';
import 'firebase/firestore';

firebase.initializeApp({
  apiKey: 'AIzaSyCnowN98wh2QAAiJMeeUHJHAE3JPXGSuNY',
  authDomain: 'awa-chat.firebaseapp.com',
  databaseURL: 'https://awa-chat.firebaseio.com',
  projectId: 'awa-chat',
  storageBucket: 'awa-chat.appspot.com',
  messagingSenderId: '426819575153',
});

const firestore = firebase.firestore();
firestore.settings({ timestampsInSnapshots: true });

@Component
export default class HelloWorld extends Vue {
  @Prop() private msg!: string;
  private user: firebase.User = null!;
  private messages: object[] = [];
  private inputMessage: string = '';

  private created() {
    firestore.collection('messages').orderBy('timestamp', 'desc').onSnapshot((querySnapShot) => {
      this.messages = [];
      querySnapShot.forEach((message) => {
        this.messages.push(message.data());
      });
    });
  }

  private async signin() {
    const result = await firebase.auth().signInWithPopup(
      new firebase.auth.GoogleAuthProvider(),
    );
    if (result.credential) {
        this.user = result.user!;
    }
  }

  private send() {
    if (this.inputMessage) {
      firestore.collection('messages').add({
        username: this.user.displayName,
        content: this.inputMessage,
        timestamp: firebase.firestore.FieldValue.serverTimestamp(),
      });
      this.inputMessage = '';
    }
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
