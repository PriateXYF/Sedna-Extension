<template>
  <div v-loading.fullscreen.lock="isLock">
    <el-button class="system-data-button" @click="$to('/')">返回首页</el-button>
    <el-card class="box-card">
      <el-form :label-position="'top'" label-width="80px" :model="formdata">
        <el-form-item label="AppID">
          <el-input v-model="formdata.appid"></el-input>
        </el-form-item>
        <el-form-item label="AppKey">
          <el-input v-model="formdata.appkey"></el-input>
        </el-form-item>
        <el-form-item label="Rust API">
          <el-input v-model="formdata.rustapi"></el-input>
        </el-form-item>
      </el-form>
      <el-button type="success" @click="bindLeancloud">Save</el-button>
    </el-card>
  </div>
</template>

<style>
.system-data-button {
  margin-top: 20px !important;
  margin-bottom: 20px !important;
  margin-right: 10px !important;
}
</style>

<script>
import AV from "leancloud-storage";
export default {
  data() {
    return {
      formdata: {
        appid: "",
        appkey: "",
        rustapi: "",
      },
      isLock: false,
    };
  },
  methods: {
    // 点击按钮绑定leancloud
    bindLeancloud() {
      var _this = this;
      this.$confirm("Binding This App？")
        .then(() => {
          _this.checkLeancloudStatue(
            _this.formdata.appid,
            _this.formdata.appkey
          );
        })
        .catch(() => {});
    },
    setLeancloud(appId, appKey) {
      var _this = this;
      var leancloud = {};
      leancloud.appid = appId;
      leancloud.appkey = appKey;
      leancloud.rustapi = this.formdata.rustapi;
      chrome.storage.sync.set({ leancloud }, function () {
        _this.$message.success("Save Success!");
      });
    },
    // 检测Leancloud状态
    checkLeancloudStatue(appId, appKey) {
      if (appId.slice(-9) !== "-MdYXbMMI") {
        this.$alert("Only Support App from us.leancloud.cn");
      } else {
        var _this = this;
        _this.isLock = true;
        AV.init({
          appId,
          appKey,
        });
        const query = new AV.Query("Config");
        query.limit(1);
        query
          .find()
          .then(() => {
            _this.isLock = false;
            _this.setLeancloud(appId, appKey);
            _this.$alert("Binding Success!");
          })
          .catch((error) => {
            _this.isLock = false;
            if (error.code == 401) {
              _this.$alert("AppId or AppKey is not correct!");
            } else if (error.code == 101) {
              _this.setLeancloud(appId, appKey);
              _this.$alert("Binding Success!");
            } else {
              _this.$alert("Error : " + error);
            }
          });
      }
    },
  },
  beforeMount() {
    var _this = this;
    chrome.storage.sync.get(
      {
        leancloud: null,
      },
      function (items) {
        var leancloud = items.leancloud;
        if (leancloud && leancloud.appid && leancloud.appkey && leancloud.rustapi) {
          _this.formdata.appid = leancloud.appid;
          _this.formdata.appkey = leancloud.appkey;
          _this.formdata.rustapi = leancloud.rustapi;
        }
      }
    );
  },
};
</script>