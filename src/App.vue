<template>
  <div class="app">
    <div class="messages-container" ref="messagesContainer">
      <div class="message" v-for="res in result" :key="res.id">
        {{ res }}
        {{ res.text }}
      </div>
      <div class="loading" v-if="loading">Загрузка</div>
      <div class="error" v-else-if="error">Ошибка</div>
    </div>
    <div class="input-container">
      <input v-model="newMessage" type="text" placeholder="Введите сообщение" />
      <button @click="sendMessage">Отправить</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      result: [],
      newMessage: '',
      offset: 0,
      loading: false,
      error: false
    };
  },
  mounted() {
    console.log('Component mounted');
    this.loadMessages();
    this.chatScrollListener();
  },
  methods: {
    loadMessages() {
      this.loading = true;
      fetch(`https://numia.ru/api/getMessages?offset=${this.offset}`)
          .then(response => response.json())
          .then(data => {
            console.log(data)
            this.result = this.result.concat(data.result);
            this.loading = false;
          })
          .catch(() => {
            this.loading = false;
            this.error = true;
          });
    },

    sendMessage() {
      if (this.newMessage) {
        const newMessage = {
          id: Date.now(),
          text: this.newMessage
        };
        this.result.push(this.newMessage);
        this.newMessage = '';
      }
    },
    chatScrollListener() {
      this.$refs.messagesContainer.addEventListener('scroll', () => {
        if (this.$refs.messagesContainer.scrollTop === 0) {
          this.offset += 10;
          this.loadMessages();
        }
      });
    }
  }
};
</script>
<style>
.app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin-top: 200px;
}
html {
  background-color: #b5e9fe;
}
body {
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: center;
}
.messages-container {
  height: 200px;
  width: 250px;
  overflow-y: auto;
  background-color: #befffa;
}
.message {
  margin-bottom: 10px;
  word-break: break-word;
}
.input-container {
  margin-top: 10px;
}
.loading {
  padding: 5px;
}
.error {
  color: red;
  padding: 5px;
}
</style>
