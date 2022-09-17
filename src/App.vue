<template>
  <div class="app">
    <div class="container">
      <h3 class="app-title my-3 pb-3">Dream Builder Guest Book Demo</h3>

      <!-- 留言表單 -->
      <form ref="msgForm" @submit.prevent>
        <div class="d-flex">
          <div class="mr-2">
            <input
              v-model="author"
              class="form-control w-auto"
              placeholder="作者"
            />
          </div>
          <div class="mr-2">
            <input
              v-model="title"
              class="form-control w-auto"
              placeholder="標題"
            />
          </div>
          <div class="mr-2">
            <select v-model="emotion" class="form-control w-aut">
              <option value="" disabled>-情緒-</option>
              <option value="happy">快樂</option>
              <option value="angry">生氣</option>
              <option value="sad">難過</option>
            </select>
          </div>
        </div>
        <div class="d-flex mt-2 mb-3 align-items-start">
          <div class="flex-grow-1 mr-2">
            <textarea
              v-model="content"
              id="content"
              class="form-control"
              placeholder="留言"
            ></textarea>
          </div>
          <button class="btn btn-primary" @click="leaveMessage">留言</button>
        </div>
      </form>
      <!-- 留言表單 -->

      <template v-if="messages.length">
        <div class="text-right text-muted">
          共 {{ messages.length }} 筆留言。
        </div>
        <div class="msg" v-for="message in messages" :key="message.id">
          <div class="d-flex">
            <h5 class="flex-grow-1">{{ message.title }}</h5>
            <div class="mr-2">
              {{ message.author }} 於 {{ message.modified }} 發表
            </div>
            <div>覺得 {{ message.emotion }}</div>
          </div>
          <hr />
          <div style="white-space: pre">{{ message.content }}</div>
          <hr />
          <div class="text-right">
            <button
              class="btn btn-danger btn-sm"
              @click="deleteMessage(message.id)"
            >
              刪除
            </button>
          </div>
        </div>
      </template>
      <div v-else class="no-data">查無資料</div>
    </div>
  </div>
</template>

<style>
.app-title {
  color: dodgerblue;
  text-align: center;
  border-bottom: 1px solid #ccc;
}

.no-data {
  text-align: center;
  font-size: 1.3rem;
}

div.msg {
  border: 1px solid #ccc;
  padding: 10px;
  margin-bottom: 1rem;
}
</style>

<script>
import "bootstrap/dist/css/bootstrap.css";
import axios from "axios";

axios.defaults.baseURL = "https://dbuilder-info.com/GBDemo";

export default {
  name: "App",

  data() {
    return {
      messages: [],
      author: "",
      title: "",
      content: "",
      emotion: "",
    };
  },

  methods: {
    async getMessages() {
      const { data } = await axios.get("/api/Messages");
      this.messages = data;
    },

    async leaveMessage() {
      const payload = {
        author: this.author,
        title: this.title,
        content: this.content,
        emotion: this.emotion,
      };

      await axios.post("/api/Messages", payload);

      this.$refs.msgForm.reset();

      this.getMessages();
    },

    async deleteMessage(id) {
      if (confirm("是否確認刪除？")) {
        await axios.delete(`/api/Messages/${id}`);
        this.getMessages();
      }
    },
  },

  async created() {
    this.getMessages();
  },
};
</script>