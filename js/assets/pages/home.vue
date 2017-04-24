<template>
  <div>
    <section v-if="!open" class="hero is-success is-fullheight">
      <div class="hero-body">
        <div class="container has-text-centered">
          <h1 class="title">
            <b>Poker</b> Chat
          </h1>
          <div class="row">
            <div class="column is-half is-offset-one-quarter">
              <div class="box">
                 <div class="field">
                  <label class="label">Typer your nickname</label>
                  <form class="control">
                    <input class="input" type="text" v-model="name">
                    <button class="button is-info" @click="submit">Ok!</button>
                  </form>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <div v-if="open" id="chat">
    <section class="hero is-primary is-medium">
      <div class="hero-head">
        <nav class="nav">
          <div class="nav-left">
            <p>
              <b>Poker</b> chat
            </p>
          </div>
        </nav>
      </div>
    </section>

     <div class="menu">
     <div class="name">{{name}}</div>
     <div class="last">{{time}}</div>
     </div>

     <div v-for="message in messages">
        <div class="box">{{message}}</div>
     </div>
     
     <input 
      @keyup.13="send"
      class="textarea" 
      type="text" 
      v-model="typed"
      placeholder="Here your message!"
      />
    </div>
  </div>
</template>

<script src="/socket.io-client/socket.io.js"></script>
<script type="text/javascript">
var socket = io.connect("http://localhost:3000")

  export default {
    name: 'Home',

    data(){
      return {
        open: false,
        name: '',
        time: '',
        messages: [],
        typed: '',
        ready: false
      }
    },

    methods: {
      submit() {
        this.open = true
        var currentTime = new Date()
        this.time = 'First login: ' + currentTime.getHours() + ':' + currentTime.getMinutes()

        socket.emit("join", this.name)
        this.ready = true

        socket.on("update", function(msg) {
          this.messages.push(msg)
        }.bind(this))
      },

      send() {
         var text = this.typed
         var time = new Date();
         
         this.messages.push(this.name + ':' + text + time.getHours() + ':' + time.getMinutes())

         socket.emit('send', text)
         this.typed = null

         socket.on('chat', function(client,msg){
          var time = new Date()
          this.messages.push(client + msg + time.getHours() + ':' + time.getMinutes())
         }.bind(this))
      }
    } 
  }
</script>