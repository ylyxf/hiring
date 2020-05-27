<template>
  <div id="app">
    <div style="text-align: left;">
      <FileButton iconCls="icon-save" @select="importCategory">导入分类</FileButton>
      <LinkButton iconCls="icon-save">导入</LinkButton>
      <LinkButton iconCls="icon-add">新增</LinkButton>
    </div>
    <BillList />
  </div>
</template>

<script>
import BillList from "./components/BillList.vue";

export default {
  name: "App",
  components: {
    BillList
  },
  methods: {
    importCategory: function(files) {
      //读取用户上传的文件内容
      var reader = new FileReader();
      reader.onloadend = function() {
        if (reader.error) {
          alert("读取文件出错");
          return;
        }
        var content = reader.result;
        console.log(content);
      };
      reader.readAsText(files[0]);
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
