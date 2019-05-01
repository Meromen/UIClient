<template>
  <div id="app">
    <div class="container-fluid h-100">
			<div class="row justify-content-center pt-5 h-100">
				<div class="col-md-4 col-xl-3 chat"><div class="card mb-sm-3 mb-md-0 contacts_card">
					<div class="card-header">
						<!-- <div class="input-group">
							<input type="text" placeholder="Search..." name="" class="form-control search">
							<div class="input-group-prepend">
								<span class="input-group-text search_btn"><i class="fas fa-search"></i></span>
							</div>
						</div> -->
            <h3 class="text-center text-white font-weight-bold">Chat list</h3>

					</div>
					<div class="card-body contacts_body">
            <ul class="contacts">
              <li v-for="(chat, index) in chats" :key="index" @click="clickChat(index)">
                <chat-tile :name="chat.name" :messages="chat.messages" :class="{'active': chat.isActive}" :messanger="chat.messanger" :status="chat.status"></chat-tile>
              </li>
            </ul>
					</div>
					<div class="card-footer"></div>
				</div></div>
				<div class="col-md-8 col-xl-6 chat">
					<div class="card">
						<div class="card-header msg_head">
							<div class="d-flex bd-highlight">
								<div class="img_cont">
									<img class="rounded-circle user_img">
									<span :class="{'online_icon': activeChat.status, 'offline': !activeChat.status  }"></span>
								</div>
								<div class="user_info">
									<span>Chat with {{activeChat.name}}</span>
									<p>{{ activeChat.messages.length }} Messages</p>
								</div>
							</div>
							<button @click="changeStatus" class="btn" :class="{'btn-success': activeChat.status > 0, 'btn-danger': !activeChat.status == 0}" id="action_menu_btn">{{ activeChat.status ? 'Выключить' : 'Включить'}} бота</button>
            </div>
						<div class="card-body msg_card_body">
							<message v-for="(msg, index) in activeChat.messages" :key="index" :message="msg.message" :sender="msg.sender"></message>
						</div>
						<div class="card-footer">
							<div class="input-group">
								<div class="input-group-append">
									<span class="input-group-text attach_btn"><i class="fas fa-paperclip"></i></span>
								</div>
								<textarea name="" v-model="messageText" class="form-control type_msg" placeholder="Type your message..."></textarea>
								<div @click="sendMessage" class="input-group-append">
									<span class="input-group-text send_btn"><i class="fas fa-location-arrow"></i></span>
								</div>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
  </div>
</template>

<script>
import chatTile from './components/ChatTile.vue'
import message from './components/Message.vue'
import { setInterval } from 'timers';

export default {
  components: {
    chatTile,
    message
  },
  data() {
    return {
      chats: [{
        'name': 'Maxim',
        'messages': [{
          'message': 'Hello, boiiiiiiiiiiiiiiiiiiiiii',
          'sender': true
        },{
          'message': 'Daun, boiiiiiiiiiiiiiiiiiiiiii',
          'sender': false
        }],
        'messanger': 'Vk',
        'status': true
      },
      {
        'name': 'Yuras',
        'messages': [{
          'message': 'wqeqweqweqweqwaaaaa',
          'sender': false
        }],
        'messanger': 'Tg',
        'status': false
      }],
      activeChat: undefined,
      messageText: '',
      uptadeMessages: undefined,
      chatsUpdate: undefined
    }
  },
  created() {
    this.chatsUpdate = setInterval(() => {
      this.axios.get('https://api.smartypanel.ru/v1/chats/?accessToken=ffefeb92a2d7ad04404fe2d44ef19bb8e0fcd863', {
        headers: {
          'Access-Control-Allow-Origin': '*'
        }}).then((res) => {
        if (res.status == 200) {
          res.data.chats.forEach((chat) => {
            this.chats.push({
              'id': chat.id,
              'name': `${chat.chat_info.firstname} ${chat.chat_info.lastname}`,
              'messages': [],
              'messanger': chat.chat_info.messanger,
              'status': chat.bot_status
            })
          }) 
        } else {
          console.log(res.err) 
        }
      }).catch(err => console.log(err))
    }, 5000)
    this.activeChat = this.chats[0]
    this.activeChat.isActive = true
     
  },
  methods: {
    clickChat(index) {
      clearInterval(this.uptadeMessages)
      this.activeChat.isActive = false
      this.activeChat = this.chats[index]
      this.chats[index].isActive = true
      this.uptadeMessages = setInterval(() => {
        this.axios.get('').then((res) => {
          this.activeChat.messages = res.data
        }).catch(err => console.log(err))
      }, 1000)

    },
    changeStatus() {
      this.axios.post('', !this.activeChat.status).then(() => {
        this.activeChat.status = !this.activeChat.status
      })
    },
    sendMessage() {
      if (this.activeChat.messanger.toLowerCase() == 'whatsapp'){
        this.axios.post('', {'message' : this.messageText, 'sender' : false}).then(() => {
          this.activeChat.messages.push({'message': this.messageText, 'sender': false})
          this.messageText = '';
        }).catch(err => console.log(err))
      } else if (this.activeChat.messanger.toLowerCase() == 'telegram'){
        this.axios.post('', {'message' : this.messageText, 'sender' : false}).then(() => {
          this.activeChat.messages.push({'message': this.messageText, 'sender': false})
          this.messageText = '';
        }).catch(err => console.log(err))
      }     
      
    }
  }
}
</script>

<style>
body,html{
  height: 100%;
  margin: 0;
  background: #7F7FD5;
  background: -webkit-linear-gradient(to right, #91EAE4, #86A8E7, #7F7FD5);
  background: linear-gradient(to right, #91EAE4, #86A8E7, #7F7FD5);
}

.text-white {
  color: #ffffff;
}

.chat{
  margin-top: auto;
  margin-bottom: auto;
}
.card{
  height: 500px;
  border-radius: 15px !important;
  background-color: rgba(0,0,0,0.4) !important;
}
.contacts_body{
  padding:  0.75rem 0 !important;
  overflow-y: auto;
  white-space: nowrap;
}
.msg_card_body{
  overflow-y: auto;
}
.card-header{
  border-radius: 15px 15px 0 0 !important;
  border-bottom: 0 !important;
}
.card-footer{
  border-radius: 0 0 15px 15px !important;
  border-top: 0 !important;
}
.container{
  align-content: center;
}
.search{
  border-radius: 15px 0 0 15px !important;
  background-color: rgba(0,0,0,0.3) !important;
  border:0 !important;
  color:white !important;
}
.search:focus{
  box-shadow:none !important;
  outline:0px !important;
}
.type_msg{
  background-color: rgba(0,0,0,0.3) !important;
  border:0 !important;
  color:white !important;
  height: 60px !important;
  overflow-y: auto;
}
.type_msg:focus{
  box-shadow:none !important;
  outline:0px !important;
}
.attach_btn{
  border-radius: 15px 0 0 15px !important;
  background-color: rgba(0,0,0,0.3) !important;
  border:0 !important;
  color: white !important;
  cursor: pointer;
}
.send_btn{
  border-radius: 0 15px 15px 0 !important;
  background-color: rgba(0,0,0,0.3) !important;
  border:0 !important;
  color: white !important;
  cursor: pointer;
}
.search_btn{
  border-radius: 0 15px 15px 0 !important;
  background-color: rgba(0,0,0,0.3) !important;
  border:0 !important;
  color: white !important;
  cursor: pointer;
}
.contacts{
  list-style: none;
  padding: 0;
}
.contacts li{
  width: 100% !important;
  padding: 5px 10px;
  margin-bottom: 15px !important;
}
.active{
  background-color: rgba(0,0,0,0.3);
}
.user_img{
  height: 70px;
  width: 70px;
  border:1.5px solid #f5f6fa;
}
.user_img_msg{
  height: 40px;
  width: 40px;
  border:1.5px solid #f5f6fa;
}
.img_cont{
  position: relative;
  height: 70px;
  width: 70px;
}
.img_cont_msg{
  height: 40px;
  width: 40px;
}
.online_icon{
  position: absolute;
  height: 15px;
  width:15px;
  background-color: #4cd137;
  border-radius: 50%;
  bottom: 0.2em;
  right: 0.4em;
  border:1.5px solid white;
}
.await_icon{
  position: absolute;
  height: 15px;
  width:15px;
  background-color: #d1cf37;
  border-radius: 50%;
  bottom: 0.2em;
  right: 0.4em;
  border:1.5px solid white;
}
.offline{
  background-color: #c23616 !important;
  position: absolute;
  height: 15px;
  width:15px;
  border-radius: 50%;
  bottom: 0.2em;
  right: 0.4em;
  border:1.5px solid white;
}
.user_info{
  margin-top: auto;
  margin-bottom: auto;
  margin-left: 15px;
}
.user_info span{
  font-size: 20px;
  color: white;
}
.user_info p{
  font-size: 10px;
  color: rgba(255,255,255,0.6);
}
.video_cam{
  margin-left: 50px;
  margin-top: 5px;
}
.video_cam span{
  color: white;
  font-size: 20px;
  cursor: pointer;
  margin-right: 20px;
}
.msg_cotainer{
  margin-top: auto;
  margin-bottom: auto;
  margin-left: 10px;
  border-radius: 25px;
  background-color: #82ccdd;
  padding: 10px;
  position: relative;
}
.msg_cotainer_send{
  margin-top: auto;
  margin-bottom: auto;
  margin-right: 10px;
  border-radius: 25px;
  background-color: #78e08f;
  padding: 10px;
  position: relative;
}
.msg_time{
  position: absolute;
  left: 0;
  bottom: -15px;
  color: rgba(255,255,255,0.5);
  font-size: 10px;
}
.msg_time_send{
  position: absolute;
  right:0;
  bottom: -15px;
  color: rgba(255,255,255,0.5);
  font-size: 10px;
}
.msg_head{
  position: relative;
}
#action_menu_btn{
  position: absolute;
  right: 10px;
  top: 10px;
  color: white;
  cursor: pointer;
  font-size: 20px;
}
.action_menu{
  z-index: 1;
  position: absolute;
  padding: 15px 0;
  background-color: rgba(0,0,0,0.5);
  color: white;
  border-radius: 15px;
  top: 30px;
  right: 15px;
  display: none;
}
.action_menu ul{
  list-style: none;
  padding: 0;
  margin: 0;
}
.action_menu ul li{
  width: 100%;
  padding: 10px 15px;
  margin-bottom: 5px;
}
.action_menu ul li i{
  padding-right: 10px;
}
.action_menu ul li:hover{
  cursor: pointer;
  background-color: rgba(0,0,0,0.2);
}
@media(max-width: 576px){
  .contacts_card{
    margin-bottom: 15px !important;
  }
}
</style>
