<template>
  <div class="container">
    <div class="chat-container">
      <div class="chat-header">
        <h3>{{ room }}</h3>
        <br>
        <hr>
        <br>
        <h5>{{ socketStatus }}</h5>
      </div>
      <div class="chat-body">
        <span style="margin-bottom: 4px;">Welcome to chat room!</span>
        <div class="chat-message" v-for="chat in chats">
          <span class="chat-message__name">{{ chat.username }}: &nbsp;</span>
          <span class="chat-message__message">{{ chat.message }}</span>
        </div>
      </div>
      <div class="chat-footer">
        <input class="chat-footer__text" type="text" id="txt-username" placeholder="Username">
        <div class="chat-footer__row">
          <textarea class="chat-footer__text" id="txt-message" cols="50" rows="5" @keypress.enter.stop="sendMessage"></textarea>
          <button class="chat-footer__button" @click="sendMessage">Send</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  async asyncData({ params }){
    roomID: params.id
    
    return {
      room: params.id,
      chats: [],
    }
  },
  data(){
    return {
      socket: null,
      socketStatus: "",
      url: "ws://localhost:8080/ws?channel=",
    }
  },
  methods: {
    connectWS(){
      this.socket = new WebSocket(this.url)
      this.socket.addEventListener('open', () => {
        this.socketStatus = "Connected..."
      })
      this.socket.addEventListener('error', (error) => {
        this.socketStatus = "Error..."
      })
      this.socket.addEventListener('message', (event) => {
        this.receiveMessage(event.data)
      })
    },
    sendMessage(){
      let txtUsername = document.querySelector('#txt-username');
      let txtMessage = document.querySelector('#txt-message');

      let username = txtUsername.value;
      let message = txtMessage.value;

      if(username == ""){
        alert("Username can't be empty!")
        return
      }else if(message == ""){
        alert("Message can't be empty!")
        return
      }else if(this.socket.readyState != WebSocket.OPEN){
        alert("Please wait until websocket is connected!")
        return
      }else{
        let chat = {
          username: username,
          message: message
        }
        /* Send data to websocket */
        this.socket.send(JSON.stringify(chat))

        /* Reset chat container view */
        txtMessage.value = ''
        let container = document.querySelector('.chat-body')
        container.scrollTop = container.scrollHeight
        return
      }
    },
    receiveMessage(data){
      let chat = JSON.parse(data)

      /* Update view */
      this.chats.push(chat)
    }
  },
  mounted(){
    this.connectWS()
    let channelQuery = $nuxt.$route.query.channel
    if(channelQuery != undefined)
      this.url += channelQuery  
    console.log(this.url)
  },
  beforeDestroy(){
    this.socket.close()
  }
}
</script>

<style lang="scss" scoped>
.container {
  max-width: 1280px;
  min-height: 100vh;
  flex-direction: column;
  margin: auto;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.chat-container{
  display: flex;
  flex-direction: column;
}

.chat-header{
  padding: 16px;
  border: 1px solid grey;
}

.chat-body{
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
  align-items: flex-start;
  height: 600px;
  overflow: auto;
  padding: 16px;
  border: 1px solid grey;
}

.chat-message{
  display: flex;
  flex-shrink: 0;
  justify-content: space-around;
  margin: 8px 0;

  &__name{

  }

  &__message{

  }
}

.chat-footer{
  display: flex;
  flex-direction: column;

  &__row{
    display: flex;
  }

  &__text{
    padding: 8px;
  }

  &__button{
    right: 0;
    width: 100px;
  }
}
</style>
