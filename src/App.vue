<template>
  <div class="app">
    <div class="container">
      <h3 class="app-title my-3 pb-3">Dream Builder Guest Book Demo</h3>

      <!-- 留言表單 -->
      <form ref="msgForm" @submit.prevent @reset.prevent="resetForm">
        <div class="d-flex">
          <div class="mr-2">
            <input
              v-model="author"
              class="form-control w-auto"
              placeholder="作者"
            />
            <small class="text-danger" v-if="!validation.author"
              >請填作者</small
            >
          </div>
          <div class="mr-2">
            <input
              v-model="title"
              class="form-control w-auto"
              placeholder="標題"
            />
            <small class="text-danger" v-if="!validation.title">請填標題</small>
          </div>
          <div class="mr-2">
            <select v-model="emotion" class="form-control w-aut">
              <option value="" disabled>-情緒-</option>
              <option value="happy">快樂</option>
              <option value="angry">生氣</option>
              <option value="sad">難過</option>
            </select>
            <small class="text-danger" v-if="!validation.emotion"
              >請選情緒</small
            >
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
            <small class="text-danger" v-if="!validation.content"
              >請填留言</small
            >
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
            <div>
              覺得
              <img
                src="./assets/happy.png"
                style="height: 2rem"
                v-if="message.emotion === 'happy'"
              />
              <img
                src="./assets/angry.png"
                style="height: 2rem"
                v-if="message.emotion === 'angry'"
              />
              <img
                src="./assets/sad.png"
                style="height: 2rem"
                v-if="message.emotion === 'sad'"
              />
            </div>
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
      validation: {
        author: true,
        title: true,
        content: true,
        emotion: true,
      },
    };
  },

  methods: {
    async getMessages() {
      const { data } = await axios.get("/api/Messages");
      this.messages = data;
    },

    validate() {
      this.validation.author = !!this.author;
      this.validation.title = !!this.title;
      this.validation.emotion = !!this.emotion;
      this.validation.content = !!this.content;

      return (
        this.validation.author &&
        this.validation.title &&
        this.validation.emotion &&
        this.validation.content
      );
    },

    clearValidation() {
      this.validation.author = true;
      this.validation.title = true;
      this.validation.emotion = true;
      this.validation.content = true;
    },

    resetForm() {
      this.author = "";
      this.title = "";
      this.emotion = "";
      this.content = "";

      this.clearValidation();
    },

    async leaveMessage() {
      if (!this.validate()) {
        return;
      }

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