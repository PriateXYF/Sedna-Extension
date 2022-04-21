<template>
  <el-container>
    <el-tabs tab-position="top">
      <el-tab-pane label="数据管理">
        <el-card class="box-card">
          <div class="text item">
            <el-link @click="$to('import')">导入数据</el-link>
          </div>
          <div class="text item">
            <el-link @click="$to('export')">导出数据</el-link>
          </div>
          <div class="text item">
            <el-link @click="$to('data')">全部数据</el-link>
          </div>
          <div class="text item">
            <el-link @click="$to('system')">系统变量</el-link>
          </div>
          <div class="text item">
            <el-link @click="$to('destroy')" type="danger">重置数据</el-link>
          </div>
        </el-card>
      </el-tab-pane>
      <el-tab-pane label="偏好管理">
        <el-card class="box-card">
          <div class="text item">
            <el-link @click="$to('like')">我的收藏</el-link>
          </div>
          <div class="text item">
            <el-link @click="$to('leancloud')">leancloud绑定</el-link>
          </div>
          <div class="text item">
            <el-link @click="activeShortcuts">激活快捷键</el-link>
          </div>
        </el-card>
      </el-tab-pane>
      <el-tab-pane label="关于">
        <el-card class="box-card">
          <div class="text item">
            该浏览器拓展由Priate创作，创作于 2022-02-19，开源于：
            <br />
            <el-link @click.native="$open('https://github.com/PriateXYF/Sedna-Extension')" type="primary" :underline="false">https://github.com/PriateXYF/Sedna-Extension</el-link>
            <br />
            此拓展与自动签到工具 Sedna 配套使用，GitHub地址：
            <br />
            <el-link @click.native="$open('https://github.com/PriateXYF/Sedna')" type="primary" :underline="false">https://github.com/PriateXYF/Sedna</el-link>
            <br />
            请在使用前先阅读说明文档：
            <br />
            <el-link @click.native="$open('http://sedna.virts.app')" type="primary" :underline="false">http://sedna.virts.app</el-link>
            <br />
            如有疑问可以联系邮箱：
            <br />
            <el-link type="primary" :underline="false">ufoquinccy@outlook.com</el-link>
            <br />
            您可以通过[爱发电]赞助我，也可以直接扫一扫微信赞赏码～
            <br />
            <el-link @click.native="$open('https://afdian.net/@cyberubbish')" type="primary" :underline="false">https://afdian.net/@cyberubbish</el-link>
            <br /><br />
            <img src="https://priate.oss-cn-beijing.aliyuncs.com/products/picture/wechat.jpg" />
            <br /><br />
            笔芯❤️❤️❤️～
          </div>
        </el-card>
      </el-tab-pane>
    </el-tabs>

  </el-container>
</template>

<style>
  html {
    width: 640px !important;
    height: 400px !important;
  }

  .text {
    font-size: 14px;
  }

  .item {
    margin-bottom: 18px;
  }

  .item img {
    width: 320px;
  }

  .clearfix:before,
  .clearfix:after {
    display: table;
    content: "";
  }

  .clearfix:after {
    clear: both
  }

  .box-card {
    width: 95%;
  }

  .el-tabs {
    width: 100%;
  }
</style>

<script>
  export default {
    data() {
      return {}
    },
    methods: {
      activeShortcuts() {
        this.$open('chrome://extensions/shortcuts')
      }
    },
    beforeMount() {
      // this.$router.push('/about')
    },
    created() {

    },
    computed: {

    }
  }
</script>