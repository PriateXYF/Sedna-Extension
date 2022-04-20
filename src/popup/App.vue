<template>
  <el-container>
    <el-header>
      <el-row class="head-text">
        <p class="head-text-line-1">{{ defaultText }}</p>
        <p class="head-text-line-2">
          <el-tag class="host-tag">{{ host }}</el-tag>
        </p>
      </el-row>
    </el-header>
    <el-main>
      <add-card v-if="leancloud" :host="host"></add-card>
      <el-alert
        v-if="!leancloud"
        style="margin-top: 32px"
        title="Error"
        type="error"
        description="Please Set Leancloud Configuration First!"
        :closable="false"
        show-icon
      >
      </el-alert>
    </el-main>
    <el-footer> </el-footer>
  </el-container>
</template>

<style>
html {
  width: 400px;
  height: 540px;
}

body {
  /* background-image: linear-gradient(#f0dfaf, #ad7b7c); */
  /* background-color: rgb(249, 210, 204); */
}

* {
  font-family: "方正书宋" !important;
  /* font-size: ; */
}

@font-face {
  font-family: "方正书宋";
  font-weight: normal;
  src: url("../assets/q-note.ttf") format("truetype");
}

.el-dialog {
  width: 80% !important;
}

.host-tag {
}

.head-text-line-1 {
  padding: 0;
  margin-block-start: 0.5em;
  margin-block-end: 0em;
  margin-inline-start: 0px;
  margin-inline-end: 0px;
}

.head-text-line-2 {
  margin-top: 0px;
  padding: 0;
  margin-block-start: 0em;
  margin-block-end: 0em;
  margin-inline-start: 0px;
  margin-inline-end: 0px;
}

.head-text {
  justify-content: center;
  align-items: center;
  text-align: center;
  font-size: 30px;
}

.text {
  font-size: 14px;
}
</style>

<script>
import addCard from "../components/AddCard";
import AV from "leancloud-storage";
export default {
  name: "App",
  components: {
    addCard,
  },
  data() {
    return {
      host: "unknown",
      leancloud: null,
    };
  },
  methods: {},
  beforeMount() {
    var _this = this;
    chrome.tabs.query(
      {
        active: true,
        lastFocusedWindow: true,
      },
      function (tab) {
        const url = new URL(tab[0].url);
        _this.host = url.host;
      }
    );
    chrome.storage.sync.get({ leancloud: null }, function (items) {
      _this.leancloud = items.leancloud;
      if (items.leancloud)
        AV.init({
          appId: items.leancloud.appid,
          appKey: items.leancloud.appkey,
        });
    });
  },
  computed: {
    defaultText() {
      return browser.i18n.getMessage("extName");
    },
  },
};
</script>