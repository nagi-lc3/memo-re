<template>
  <div class="flex">
    <div class="fixedGlobal">
      <GlobalHeader />
    </div>
    <div class="preOWrap">
      <div class="flex preOverWrap">
        <div class="preWrap">
          <div class="flex innerWrap">
            <p class="title">{{ title }}</p>
          </div>
          <ul class="flex" style="flex-wrap: wrap;">
            <li v-for="word in keywordAry" v-bind:key="word" class="keyword" style=" margin-bottom: 6px;">
              {{ word }}
            </li>
          </ul>
          <p class="text_uri">{{ text_uri }}</p>
        </div>
        <div class="preImg">
          <img :src="imgSrc" alt="画像" />
        </div>
      </div>
      <div class="flex buttonWrap">
        <router-link to="/createImage" class="button preButton"
          >戻る</router-link
        >
        <button class="button" @click="CreateNote">ノート作成</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { API_SERVER } from "@/assets/config.js";
import GlobalHeader from "@/components/GlobalHeader.vue";

export default {
  name: "PreviewImage",
  components: {
    GlobalHeader,
  },
  data() {
    return {
      user: localStorage.getItem("id"),
      title: localStorage.getItem("title"),
      keyword: localStorage.getItem("keyword"),
      text_uri: localStorage.getItem("text_uri"),
      image_uri: localStorage.getItem("image_uri"),
      is_public: false,
      keywordAry: [],
    };
  },
  methods: {
    CreateNote: async function () {
      const requestBody = {
        user: this.user,
        title: this.title,
        keyword: this.keyword,
        text_uri: this.text_uri,
        image_uri: this.image_uri,
        is_public: this.is_public,
      };
      console.log(requestBody);
      const token = this.$cookies.get("access");
      await axios
        .post(API_SERVER + "/api/v1/brains/", requestBody, {
          headers: { Authorization: "JWT " + token },
        })
        .then(() => {
          console.log("成功");
          this.$router.push({ name: "myPage" });
          return;
        })
        .catch((e) => {
          console.log(e);
          return;
        });
    },
  },
  computed: {
    imgSrc() {
      const img = "/media/brain/" + this.image_uri;
      return img;
    },
  },
  created() {
    if (this.$cookies.get("access") === null) {
      const refresh = this.$cookies.get("refresh");
      if (refresh != null) {
        console.log("リフレッシュ");
        const requestBody = {
          refresh: refresh,
        };
        axios
          .post(API_SERVER + "/api/v1/auth/jwt/refresh/", requestBody)
          .then((response) => {
            let token = response.data.access;
            this.$cookies.set("access", token, 60 * 30);
            location.reload();
          })
          .catch(() => {
            //エラー回避用
            this.$router.push("/SignIn");
          });
      }else{
        this.$router.push("/SignIn");
      }
      return;
    }
    localStorage.removeItem("title");
    localStorage.removeItem("keyword");
    localStorage.removeItem("text_uri");
    localStorage.removeItem("image_uri");
    this.keywordAry = this.keyword.split(",");
    console.log(this.keywordAry);
  },
};
</script>

<style scoped>
.preOWrap {
  margin: 0 auto;
  padding-top: 100px;
  width: 900px;
}
.preOverWrap {
  justify-content: space-between;
  flex-wrap: wrap;
}
.preWrap {
  width: 400px;
}
.innerWrap {
  justify-content: space-between;
  flex-wrap: wrap;
}
.title {
  font-size: 28px;
}
.text_uri {
  font-size: 18px;
}
.keyword {
  color: #fff;
  font-size: 16px;
  background: #6d8dff;
  padding: 4px 8px;
  margin-right: 10px;
  border-radius: 5px;
}
.text_uri {
  width: 400px;
  word-wrap: break-word;
  margin-top: 60px;
  line-height: 24px;
}
.preImg {
  width: 360px;
}
.preImg img {
  width: 100%;
  border-radius: 20px;
  box-shadow: 8px 6px 8px 3px #999;
}
.buttonWrap {
  margin: 60px auto 0;
  width: 360px;
  justify-content: space-between;
  flex-wrap: wrap;
}
.button {
  float: right;
  color: #fff;
  background: #1e4fff;
  background: #1e4fff;
  width: 160px;
  height: 54px;
  text-align: center;
  border-radius: 5px;
  line-height: 54px;
}
.button:hover{
  background: #0015ff;
}
.button:hover{
  background: #0015ff;
}
.preButton {
  background: #ff78f4;
}
.preButton:hover {
  background: #ff32b1;
  background: #ff78f4;
}
.preButton:hover {
  background: #ff32b1;
}
</style>