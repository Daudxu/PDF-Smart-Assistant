<script setup>
import { ref } from "vue";
import Chat from "@/components/Chat.vue";
const question = ref("");
const wrapper = ref([]);
const loading = ref(false);
const baseUrl = "http://localhost:1573"
document.onkeydown = function (e) {
   let key = (window).event.keyCode;
  if (key === 13) {
     if(question.value && question.value !== ""){
      fetchAnswer()
     }
  }
 }

const fetchAnswer = async () => {
  if(question.value && question.value !== ""){
    try {
    loading.value = true;
    wrapper.value.push({
      isAi: false,
      value: question.value,
    });
    wrapper.value.push({
      isAi: true,
      value: "Loading....",
    });
    const res = await fetch(baseUrl, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        question: question.value,
      }),
    });
    // console.log(res);
    const data = await res.json();
    console.log(data);
    const parsedData = data.bot.trim();
    wrapper.value.pop();
    wrapper.value.push({
      isAi: true,
      value: parsedData,
    });
  } catch (error) {
  } finally {
    loading.value = false;
    question.value = ''
  }
  }

 
};
</script>

<template>
  <main>
    <div id="chat_container">
      <div
        v-for="(chat, i) in wrapper"
        :key="i"
        class="wrapper"
        :class="{ ai: chat.isAi }"
      >
        <Chat :chat="chat" :key="i" />
      </div>
    </div>
    <form @submit.prevent="fetchAnswer">
      <textarea :disabled="loading"
        rows="1"
        cols="1"
        placeholder="Please enter your question..."
        v-model="question"
      ></textarea>
      <button type="submit"><img src="@/assets/send.svg" alt="send" /></button>
    </form>
  </main>
</template>
