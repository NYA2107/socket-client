<template>
  <div id="app">
    <div v-if="!openChat" style="padding-top:200px;width: 100%;display: flex;justify-content: center;align-items: flex-start;flex-wrap: wrap;">
      <b-field style="width:60%"  type="is-warning">
          <b-input v-model="name" placeholder="UHUYY .. Enter Your Name"
              type="text">
          </b-input>
      </b-field>
      <button @click="register" style="width:60%" class="button is-medium is-warning">
          Submit
      </button>
    </div>
    <div v-if="openChat" style="width:80%;padding-top: 20px;">
      <p style="text-align: right;padding: 20px;background-color: #75714c;color:#ffea2d;"><strong style="color:#ffea2d;">{{name}}</strong></p>
      <div style="height: 70vh;width:100%;background-color: #eae1cc;padding: 20px;overflow: auto;">
        <p v-for="chat in chat" style="margin:10px;background-color: white; width: auto;padding:10px;"><strong>{{chat.name}} : </strong> {{chat.msg}}</p>
        <p style="color:#82704b;margin: 20px;" v-if="feedback != ''">{{feedback}} is Typing ...</p>
      </div>
      <b-field style="margin-top: 20px;" type="is-warning">
          <b-input v-model="msg"  placeholder="Type your msg here .."
              type="text">
          </b-input>
      </b-field>
      <button @click="sendMsg()" style="width:100%" class="button is-medium is-warning">
          Send
      </button>
    </div>
  </div>
</template>

<script>
import io from 'socket.io-client';
export default {
  data(){
    return({
      name:'',
      msg:'',
      feedback:'',
      chat:[],
      openChat:false
    })
  },
  created(){
    this.socket = io.connect('https://quiet-garden-62218.herokuapp.com/')
  },
  mounted(){
    this.socket.on('getMsg',(response)=>{
      this.feedback = ''
      this.chat.push(response)
    })
    this.socket.on('imTyping',(response)=>{
      this.feedback = response
    })
  },
  methods:{
    register(){
      if(this.name != ""){
        this.openChat=true  
      }
    },
    sendMsg(){
      this.socket.emit('sendMsg',{
        msg:this.msg,
        name:this.name
      })
      this.msg = ''
    }
  },
  watch:{
    msg(newVal){
      if(newVal != ''){
        this.socket.emit('typing',this.name)  
      }
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}
/* width */
::-webkit-scrollbar {
  width: 5px;
}

/* Track */
::-webkit-scrollbar-track {
  box-shadow: inset 0 0 5px grey; 
  border-radius: 10px;
}
 
/* Handle */
::-webkit-scrollbar-thumb {
  background: grey; 
  border-radius: 10px;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: black; 
}
</style>
