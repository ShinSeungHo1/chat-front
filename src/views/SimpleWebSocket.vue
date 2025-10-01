<template>
  <v-container>
    <v-row justify="center">
      <v-col cols="12" md="8">
        <v-card>
          <v-card-title class="text-center text-h5"> 채팅 </v-card-title>
          <v-card-text>
            <div class="chat-box">
              <div v-for="(message, index) in messages" :key="index">
                {{ message }}
              </div>
            </div>
            <v-text-field
              v-model="newMessage"
              label="메시지 입력"
              @keyup.enter="sendMeesage"
            />
            <v-btn color="primary" block @click="sendMeesage">전송</v-btn>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
export default {
  data() {
    return {
      ws: null,
      messages: [],
      newMessage: "",
    };
  },
  created() {
    if (this.ws === null) {
      this.connectWebSocket();
    }
  },
  beforeUnmount() {
    this.disconnectWebSocket();
  },
  methods: {
    connectWebSocket() {
      this.ws = new WebSocket(`ws://localhost:8080/connect`);

      this.ws.onopen = () => {
        console.log("Successfully Connected!!");
      };
      this.ws.onmessage = (message) => {
        this.messages.push(message.data);
        this.scrollToBottom();
      };
      this.ws.onclose = () => {
        console.log("Disconnected!!!!");
      };
    },
    sendMeesage() {
      if (this.newMessage.trim() === "") {
        return;
      }
      this.ws.send(this.newMessage);
      this.newMessage = "";
    },
    scrollToBottom() {
      this.$nextTick(() => {
        const chatBox = this.$el.querySelector(".chat-box");
        chatBox.scrollTop = chatBox.scrollHeight;
      });
    },
    disconnectWebSocket() {
      if (this.ws) {
        this.ws.close();
        console.log("DisConnected!!!!");
        this.ws = null;
      }
    },
  },
};
</script>

<style>
.chat-box {
  height: 300px;
  overflow-y: auto;
  border: 1px solid #ddd;
  margin-bottom: 10px;
}
</style>
