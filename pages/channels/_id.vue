<template>
  <div class="container">
    <div class="chats-layout">
      <Messages :messages="messages" />
    </div>
    <ChatForm />
  </div>
</template>
<script>
import { db } from "~/plugins/firebase";
export default {
  data() {
    return {
      messages: [],
    };
  },
  mounted() {
    const channelId = this.$route.params.id;
    db.collection("channels")
      .doc(channelId)
      .collection("messages")
      .orderBy("createdAt")
      .onSnapshot((snapshot) => {
        snapshot.docChanges().forEach((change) => {
          const doc = change.doc;
          if (change.type === "added") {
            this.messages.push({ id: doc.id, ...doc.data() });
          }
        });
      });
  },
};
</script>

<style scoped>
.container {
  height: 100%;
}

.chats-layout {
  overflow: scroll;
  height: 90%;
}
</style>
