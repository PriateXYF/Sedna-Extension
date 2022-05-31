<template>
  <div class="add-card">
    <el-col
      :xs="24"
      :sm="12"
      :md="8"
      :lg="6"
      :xl="4"
      v-show="!isSave"
      v-loading="isLoading"
    >
      <el-card class="indexcontainer card add-note-card" shadow="hover">
        <div style="padding: 14px">
          <el-form :model="form" ref="modifyForm">
            <el-form-item>
              <el-row>
                <el-col :span="4"><span>Name</span></el-col>
                <el-col :span="20"
                  ><el-input v-model="form.name"></el-input
                ></el-col>
              </el-row>
            </el-form-item>
            <el-form-item>
              <el-row>
                <el-col :span="4"><span>URL</span></el-col>
                <el-col :span="20"
                  ><el-input v-model="form.url"></el-input
                ></el-col>
              </el-row>
            </el-form-item>
            <el-form-item label="Failure Notification">
              <el-switch v-model="form.isNotification"></el-switch>
            </el-form-item>
            <el-form-item label="Method">
              <el-radio-group v-model="form.method">
                <el-radio label="GET"></el-radio>
                <el-radio label="POST"></el-radio>
              </el-radio-group>
            </el-form-item>
            <el-divider content-position="left">Headers</el-divider>
            <el-form-item
              v-for="(header, index) in form.headers"
              :key="index + 100"
            >
              <el-row>
                <el-col :span="8"
                  ><el-input placeholder="Key" v-model="header.key"></el-input
                ></el-col>
                <el-col :span="1"><div>:</div></el-col>
                <el-col :span="15"
                  ><el-input
                    placeholder="Value"
                    v-model="header.value"
                    @input="inputHeader"
                  ></el-input
                ></el-col>
              </el-row>
            </el-form-item>
            <el-divider content-position="left">Bodies</el-divider>
            <el-form-item v-for="(body, index) in form.bodies" :key="index">
              <el-row>
                <el-col :span="8"
                  ><el-input placeholder="Key" v-model="body.key"></el-input
                ></el-col>
                <el-col :span="1"><div>:</div></el-col>
                <el-col :span="15"
                  ><el-input
                    placeholder="Value"
                    v-model="body.value"
                    @input="inputBody"
                  ></el-input
                ></el-col>
              </el-row>
            </el-form-item>
            <el-divider content-position="left">Result</el-divider>
            <el-form-item>
              <el-row>
                <el-col :span="6"><span>Success :</span></el-col>
                <el-col :span="18"
                  ><el-input v-model="form.result.success"></el-input
                ></el-col>
              </el-row>
            </el-form-item>
            <el-form-item>
              <el-row>
                <el-col :span="6"><span>Failure :</span></el-col>
                <el-col :span="18"
                  ><el-input v-model="form.result.failure"></el-input
                ></el-col>
              </el-row>
            </el-form-item>
            <el-form-item>
              <el-row>
                <el-col :span="6"><span>Repete :</span></el-col>
                <el-col :span="18"
                  ><el-input v-model="form.result.repete"></el-input
                ></el-col>
              </el-row>
            </el-form-item>
            <el-form-item>
              <el-button type="success" @click="saveOnlineForm"
                >Save from Online</el-button
              >
              <el-button type="primary" @click="uploadConfig">Save</el-button>
            </el-form-item>
          </el-form>
          <div class="bottom clearfix"></div>
        </div>
      </el-card>
    </el-col>
    <el-col
      :xs="24"
      :sm="12"
      :md="8"
      :lg="6"
      :xl="4"
      v-show="isSave"
      v-loading="isLoading"
    >
      <el-card class="indexcontainer card add-note-card" shadow="hover">
        <div class="operation-buttons">
          <el-row>
            <el-col :span="12"
              ><el-button
                type="warning"
                plain
                style="width: 100%"
                @click="isSave = !isSave"
                >Modify</el-button
              ></el-col
            >
            <!-- <el-col :span="8"><el-button style="width: 100%" @click="copyConfig">Copy</el-button></el-col> -->
            <el-col :span="12"
              ><el-button
                type="success"
                plain
                style="width: 100%"
                @click="checkinOnline"
                >Checkin</el-button
              ></el-col
            >
          </el-row>
          <el-row>
            <el-col :span="12"
              ><el-button
                type="danger"
                plain
                style="width: 100%"
                @click="deleteConfig"
                >Delete</el-button
              >
            </el-col>
            <el-col :span="12"
              ><el-button
                type="info"
                plain
                style="width: 100%"
                @click="showInfo"
                >Info</el-button
              ></el-col
            >
          </el-row>
        </div>
      </el-card>
      <div v-show="isShowInfo">
        <el-alert
          @click.native="
            copyToClipboard(
              JSON.stringify({
                id: cloudConfig.id,
              })
            )
          "
          :title="cloudConfig ? `id : ${cloudConfig.id}` : 'id undefined'"
          center
          :style="{ cursor: 'pointer' }"
          :closable="false"
        >
        </el-alert>
        <el-alert
          v-for="log in logs"
          :key="log.id"
          :title="`${dayjs(log.get('createdAt')).format(
            'YY-MM-DD HH:mm'
          )} - ${log.get('status')}`"
          :type="formatLogStatus(log.get('status'))"
          center
          show-icon
          :closable="false"
        >
        </el-alert>
      </div>
    </el-col>
  </div>
</template>
<style>
.add-card {
  margin-top: 32px;
}
.card {
  /* background-color: rgb(255, 230, 226) !important; */
  /* border: 2px solid black !important; */
  border-width: 2px !important;
}

.operation-buttons button {
  margin: 4px;
}
</style>

<script>
import _ from "lodash";
import axios from "axios";
import AV from "leancloud-storage";
import dayjs from "dayjs";
export default {
  props: ["host"],
  data() {
    return {
      dayjs: dayjs,
      isSave: false,
      isLoading: false,
      isShowInfo: false,
      form: {
        name: "",
        url: "",
        isNotification: false,
        method: "POST",
        headers: [{ key: "", value: "" }],
        bodies: [{ key: "", value: "" }],
        result: {
          repete: "",
          failure: "",
          success: "",
        },
      },
      config: null,
      cloudConfig: null,
      logs: [],
    };
  },
  components: {},
  methods: {
    async getCookie() {
      var cookieStr;
      const _this = this;
      const cookies = await browser.cookies.getAll({
        domain: _this.host,
      });
      cookieStr = cookies.map((c) => c.name + "=" + c.value).join(";");
      return cookieStr;
    },
    async getElement(code) {
      const res = await browser.tabs.executeScript({
        code: code,
      });
      return res[0];
    },
    inputHeader() {
      var lastHeader = this.form.headers[this.form.headers.length - 1];
      if (lastHeader.key && lastHeader.value)
        this.form.headers.push({
          key: "",
          value: "",
        });
    },
    inputBody() {
      var lastBody = this.form.bodies[this.form.bodies.length - 1];
      if (lastBody.key && lastBody.value)
        this.form.bodies.push({
          key: "",
          value: "",
        });
    },
    saveOnlineForm() {
      this.isLoading = true;
      const _this = this;
      axios
        .get(`https://sedna.virts.app/api/${this.host}.json`)
        .then((res) => {
          _this.$message.success("Load Success!");
          const data = _.get(res, "data", null);
          if (data) _this.form = data;
        })
        .catch(() => {
          _this.$message.error("Load Failure!");
        })
        .finally(() => {
          _this.isLoading = false;
        });
    },
    // 生成配置文件
    async generateConfig() {
      this.config = _.cloneDeep(this.form);
      this.config.headers = {};
      this.config.bodies = {};
      for (const index in this.form.headers) {
        if (
          !!this.form.headers[index].key &&
          !!this.form.headers[index].value
        ) {
          switch (this.form.headers[index].key.trim().toLowerCase()) {
            case "cookie":
              this.config.headers[this.form.headers[index].key.trim()] =
                (await this.getCookie()) || "";
              break;
            default:
              this.config.headers[this.form.headers[index].key.trim()] =
                (await this.getElement(this.form.headers[index].value)) || "";
              break;
          }
        }
      }
      for (const index in this.form.bodies) {
        if (!!this.form.bodies[index].key && !!this.form.bodies[index].value) {
          this.config.bodies[this.form.bodies[index].key.trim()] =
            (await this.getElement(this.form.bodies[index].value)) || "";
        }
      }
      return this.config;
    },
    copyToClipboard(text) {
      if (text === undefined) return;
      const tempScrollTop = document.scrollingElement.scrollTop
      var copyDiv = document.createElement("textarea");
      document.body.appendChild(copyDiv, document.body.firstChild);
      copyDiv.innerText = text;
      copyDiv.focus();
      copyDiv.select();
      document.execCommand("copy");
      document.body.removeChild(copyDiv);
      document.scrollingElement.scrollTop = tempScrollTop
      this.$message.success("Copy Success!");
    },
    async copyConfig() {
      if (!this.config) await this.generateConfig();
      this.copyToClipboard(JSON.stringify(this.config));
    },
    async uploadConfig() {
      const _this = this;
      this.isLoading = true;
      if (!this.config) await this.generateConfig();
      if (this.cloudConfig) {
        this.cloudConfig.set("config", _this.config);
        this.cloudConfig.set("rawConfig", _this.form);
        this.cloudConfig
          .save()
          .then(
            (config) => {
              _this.cloudConfig = config;
              _this.$message.success("Upload Success!");
              _this.isSave = !_this.isSave;
            },
            (error) => {
              console.log(error);
              _this.$message.error("Upload Failure!");
            }
          )
          .finally(() => {
            _this.isLoading = false;
          });
      } else {
        const Config = AV.Object.extend("Config");
        const config = new Config();
        config.set("config", _this.config);
        config.set("host", _this.host);
        config.set("rawConfig", _this.form);
        config
          .save()
          .then(
            (config) => {
              _this.cloudConfig = config;
              _this.$message.success("Upload Success!");
            },
            (error) => {
              console.log(error);
              _this.$message.error("Upload Failure!");
            }
          )
          .finally(() => {
            _this.isLoading = false;
          });
      }
    },
    // 删除云端数据
    deleteCloudConfig() {
      const _this = this;
      if (this.cloudConfig) {
        this.isLoading = true;
        this.cloudConfig
          .destroy()
          .then(
            () => {
              _this.$message.success("Delete Cloud Config Success!");
            },
            (error) => {
              console.log(error);
              _this.$message.error("Delete Cloud Config Failure!");
            }
          )
          .finally(() => {
            _this.isLoading = false;
          });
      } else {
        this.$message.success("Delete Cloud Config Success!");
      }
    },
    // 删除本地数据
    deleteLocalConfig() {
      const _this = this;
      chrome.storage.sync.get({ data: {} }, function (items) {
        var data = items.data;
        if (Object.hasOwnProperty.call(data, _this.host)) {
          delete data[_this.host];
          chrome.storage.sync.set({ data }, function () {
            _this.$message.success("Delete Local Config Success!");
          });
        }
      });
      this.config = null;
      this.form = {
        name: "",
        url: "",
        isNotification: false,
        method: "POST",
        headers: [{ key: "", value: "" }],
        bodies: [{ key: "", value: "" }],
        result: {
          success: "",
          failure: "",
          repete: "",
        },
      };
      this.isSave = false;
    },
    deleteConfig() {
      this.deleteCloudConfig();
      this.deleteLocalConfig();
    },
    checkinOnline() {
      if (!this.cloudConfig)
        return this.$message.error("Config Need Upload First!");
      const _this = this;
      chrome.storage.sync.get({ leancloud: {} }, function (items) {
        const leancloud = items.leancloud;
        const rustapi = _.get(leancloud, "rustapi", null);
        const appid = _.get(leancloud, "appid", null);
        const appkey = _.get(leancloud, "appkey", null);
        if (rustapi && appid && appkey) {
          _this.isLoading = true;
          axios
            .post(
              `${rustapi}/1.1/functions/checkin`,
              { id: _this.cloudConfig.id },
              {
                headers: {
                  "X-LC-Id": appid,
                  "X-LC-Key": appkey,
                },
              }
            )
            .then((res) => {
              _this.$message.success(
                `Call Cloud Function Success! Result : ${_.get(
                  res,
                  "data.result.status",
                  "unknown"
                )}`
              );
            })
            .catch((error) => {
              console.log(error);
              _this.$message.error("Call Cloud Function Error!");
            })
            .finally(() => (_this.isLoading = false));
        } else {
          this.$message.error("Set Leancloud Configuration First!");
        }
      });
    },
    // 显示基本信息 日志+id
    showInfo() {
      this.isLoading = true;
      const _this = this;
      const query = new AV.Query("Log");
      query
        .equalTo("config", this.cloudConfig)
        .descending("createdAt")
        .limit(10)
        .find()
        .then((logs) => {
          console.log(logs);
          _this.logs = logs;
          // _this.form = config.get("rawConfig") || _this.form;
          // _this.isSave = true;
        })
        .catch((error) => {
          // _this.$message.error("Sync Cloud Config Error!");
          console.log(error);
        })
        .finally(() => {
          _this.isLoading = false;
          _this.isShowInfo = true;
        });
    },
    formatLogStatus(status) {
      const statusMap = {
        error: "error",
        success: "success",
        repete: "warning",
        failure: "error",
      };
      return statusMap[status];
    },
  },
  async created() {
    const _this = this;
    this.isLoading = true;
    // 同步表单数据
    // chrome.storage.sync.get({ data: {} }, function (items) {
    //   var data = items.data;
    //   if (Object.hasOwnProperty.call(data, _this.host)) {
    //     _this.form = data[_this.host];
    //     _this.isSave = true;
    //   }
    // });
    // 同步云端数据
    const query = new AV.Query("Config");
    query.equalTo("host", this.host);
    query
      .first()
      .then((config) => {
        _this.cloudConfig = config;
        _this.form = config.get("rawConfig") || _this.form;
        _this.isSave = true;
        _this.showInfo()
      })
      .catch((error) => {
        _this.$message.error("Sync Cloud Config Error!");
        console.log(error);
      })
      .finally(() => {
        _this.isLoading = false;
      });
  },
};
</script>