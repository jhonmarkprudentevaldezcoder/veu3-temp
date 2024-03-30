<template>
  <div class="container mx-auto sticky top-0 w-full h-full">
    <div class="chatbox fixed bottom-10 md:right-10 ">
      <div class="chatbox__support  flex flex-col bg-gray-200 w-72 h-96 z-50 opacity-0 transition-opacity duration-500 ease-in-out mb-6  bg-gray-100 h-[450px] w-[350px] shadow-lg "
     :class="{ 'chatbox--active': state }"
     :style="state ? 'z-index: 123456; opacity: 1;' : ''">

        <div class="chatbox__header sticky top-0 bg-orange bg-gradient-to-r from-[#0a0661] to-[#210d8f] flex items-center justify-center px-5 py-3 shadow-md">
          <div class="chatbox__image--header mr-2">
            <img :src="Icon" width="25px" height="auto" alt="image">
          </div>
          <div class="chatbox__content--header text-white text-sm">
            <h4 class="chatbox__heading--header ">DSWD Chat support</h4>
            <p class="chatbox__description--header " v-if="!loading">Hi. My name is Mark. How can I help you?</p>
          </div>
        </div>

        <div class="chatbox__messages chatbox__messages mt-auto flex flex-col-reverse overflow-y-scroll px-5">
          <div v-for="(message, index) in messages.slice().reverse()" :key="index" :class="message.name === 'Dswd' ? ' bg-gray-300 py-2 px-3 rounded-tl-lg rounded-tr-lg rounded-br-lg bg-orange max-w-60.6 w-max messages__item mt-4 bg-gray-300 py-2 px-3 max-w-[70%] messages__item--visitor mr-auto' : 'mt-4 bg-gray-300 py-2 px-3 max-w-[70%] messages__item messages__item--operator ml-auto bg-purple-700 text-white py-2 px-3'">{{ message.message }}</div>
        </div>

        <div class="chatbox__footer sticky bottom-0 flex items-center justify-between py-2 px-2 bg-[#680808] shadow-xl mt-4">
          <input type="text" placeholder="Write a message..." v-model="newMessage" class="w-5/6 bg-white border-none py-2 px-3 text-left">
          <button class="chatbox__send--footer text-white send__button p-1 bg-transparent border-none outline-none cursor-pointer send__button" @click="sendMessage"><img :src="sendIcon" width="25px" height="auto" alt="text"/></button>
        </div>
      </div>
      <div class="chatbox__button flex justify-end p-2">
        <button @click="toggleState"><img :src="MessageIcon" width="35px" height="auto" alt="text"/></button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import MessageIcon from '@/assets/images/mail.png'
import sendIcon from '@/assets/images/send-mail.png'
import Icon from '@/assets/images/emblem.png'

const state = ref(false);
const messages = ref([]);
const newMessage = ref('');
const loading = ref(false);

const toggleState = () => {
  state.value = !state.value;
};

const sendMessage = () => {
  if (newMessage.value === '') {
    return;
  }
  const msg1 = { name: "User", message: newMessage.value };
  messages.value.push(msg1);

  fetch('http://127.0.0.1:5000/predict', {
    method: 'POST',
    body: JSON.stringify({ message: newMessage.value }),
    mode: 'cors',
    headers: {
      'Content-Type': 'application/json'
    },
  })
  .then(r => r.json())
  .then(r => {
    const msg2 = { name: "Dswd", message: r.answer };
    messages.value.push(msg2);
    newMessage.value = '';
    loading.value = false;
  })
  .catch((error) => {
    console.error('Error:', error);
    newMessage.value = '';
    loading.value = false;
  });
};
</script>

