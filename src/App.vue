<template>
  <!-- <WebsocketDemo /> -->
  <login v-if="!isLogin" @submit="joinChat" />
  <chatRoomCon v-else ref="chatRoomRef" />
</template>
<script setup lang="ts">
// import WebsocketDemo from '@/components/WebsocketDemo.vue'
import { ref } from 'vue'
import login from '@/components/Login.vue'
import chatRoomCon from '@/components/ChatRoom.vue'
import socket from '@/utils/socket'
import chatRoomStore from '@/store/chatRoom'

const chatRoom = chatRoomStore()
const chatRoomRef = ref()

const isLogin = ref(false)
const joinChat = (user: any) => {
  socket.emit('login', {
    userName: user.userName,
    avator: user.avator
  })
}

socket.on('loginError', (data: any) => {
  alert(data.msg)
})

socket.on('loginSuccess', (myself: any) => {
  isLogin.value = true
  chatRoom.chatData.myself = myself
  alert('登录成功')
})

// 保存聊天信息
socket.on('addUser', (msg: string) => {
  chatRoom.chatInfo.push({ systemMsg: msg })
  chatRoomRef.value.scrollToBootm()
})

// 保存聊天信息
socket.on('userList', (users: any) => {
  chatRoom.chatData.users = users
  chatRoom.chatData.count = users.length
})

// 退出聊天室
socket.on('delUser', (user: any) => {
  chatRoom.chatInfo.push({ systemMsg: `${user.userName}离开了聊天室` })
  chatRoomRef.value.scrollToBootm()
})

// 发送消息
socket.on('recieveMsg', (data: any) => {
  const { myself } = chatRoom.chatData
  if (myself.userName === data.userName) {
    chatRoom.chatInfo.push({ myMsg: data })
  } else {
    chatRoom.chatInfo.push({ otherMsg: data })
  }
  chatRoomRef.value.scrollToBootm()
})
</script>
<style>
* {
  box-sizing: border-box;
}
input,
button,
select,
textarea {
  outline: none;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  margin: 0 auto;
  text-align: left;
}
body {
  background: #4493cb;
}
</style>
