<template>
  <div>
    <el-button class="system-data-button" @click="$to('/')">返回首页</el-button>
    <el-button
      class="system-data-button"
      @click="refreshSystemData"
      type="primary"
      >刷新</el-button
    >
    <el-table :data="data" stripe style="width: 100%">
      <el-table-column prop="name" label="Domain" width="140"></el-table-column>
      <el-table-column prop="value" label="Value"> </el-table-column>
    </el-table>
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
export default {
  data() {
    return {
      data: [],
    };
  },
  methods: {
    refreshSystemData() {
      this.data = [];
      var _this = this;
      chrome.storage.sync.get({ data: {} }, function (items) {
        const data = items.data;
        for (const key in data) {
          if (Object.hasOwnProperty.call(data, key)) {
            const element = data[key];
            _this.data.push({
              name: key,
              value: JSON.stringify(element)
            })
          }
        }
      });
    },
  },
  beforeMount() {
    this.refreshSystemData();
  },
};
</script>