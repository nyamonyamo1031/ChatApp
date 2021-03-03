<template>
  <div class="chats-container">
    <Message v-for="message in messages" :message="message" :key="message.id" />
  </div>
</template>
<script>
import { db } from "~/plugins/firebase";
export default {
  props: ["messages"],
  mounted() {
    console.log(this.messages);
  },
};
</script>
<style scoped>
.chats-container {
  padding: 16px;
}
</style>
