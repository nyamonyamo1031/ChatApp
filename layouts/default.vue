<template>
  <div class="app-layout">
    <div class="sidebar">
      <p>チャンネル一覧</p>
      <ul>
        <li v-for="channel in channels" :key="channel.id">
          <nuxt-link :to="`/channels/${channel.id}`">{{
            channel.name
          }}</nuxt-link>
        </li>
      </ul>
      <p v-if="isAuthenticated" class="logout" v-on:click="logout">
        ログアウト
      </p>
    </div>
    <div class="main-content">
      <Nuxt />
    </div>
  </div>
</template>

<script>
import { db, firebase } from "~/plugins/firebase";
import { mapActions } from "vuex";
export default {
  data() {
    return {
      channels: [],
    };
  },
  computed: {
    isAuthenticated() {
      return this.$store.getters.isAuthenticated;
    },
  },
  methods: {
    ...mapActions(["setUser"]),
    logout() {
      firebase
        .auth()
        .signOut()
        .then(() => {
          this.setUser(null);
          window.alert("ログアウトに成功！");
        })
        .catch((e) => {
          window.alert("ログアウトに失敗しました");
          console.log(e);
        });
    },
  },
  mounted() {
    firebase.auth().onAuthStateChanged((user) => {
      if (user) {
        this.setUser(user);
      }
    });
    db.collection("channels")
      .get()
      .then((querySnapshot) => {
        querySnapshot.forEach((doc) => {
          this.channels.push({ id: doc.id, ...doc.data() });
        });
      });
  },
};
</script>

<style>
html {
  font-family: "Source Sans Pro", -apple-system, BlinkMacSystemFont, "Segoe UI",
    Roboto, "Helvetica Neue", Arial, sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
}

*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
}

a {
  text-decoration: none;
  color: #dddddd;
}

.app-layout {
  display: flex;
}

.sidebar {
  width: 300px;
  background: #2c3f55;
  height: 100vh;
  padding: 20px;
}

.sidebar p {
  color: #dddddd;
  margin-block-end: 8px;
  font-weight: bold;
}

.sidebar ul li {
  list-style-type: none;
  margin-block-end: 4px;
}

.main-content {
  width: 100%;
  background: #eeeae4;
  height: 100vh;
}

.logout {
  position: absolute;
  bottom: 10px;
  cursor: pointer;
}
</style>
