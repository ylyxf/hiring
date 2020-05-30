<template>
  <div>
    <FileButton iconCls="icon-save" @select="importCategory">导入分类</FileButton>
    <DataGrid idField="id" :data="categoryList" style="height:250px">
      <GridColumn field="name" title="Name"></GridColumn>
      <GridColumn field="type" title="Type">
        <template slot="body" slot-scope="scope">{{scope.row.type==0?"支出":"收入"}}</template>
      </GridColumn>
    </DataGrid>
  </div>
</template>

<script>
import Papa from "papaparse";

export default {
  data() {
    return {
      categoryList: []
    };
  },
  methods: {
    importCategory: function(files) {
      console.log("in method:", this.categoryList);
      // this can't be used in callback functions,defind _data to hold this
      let _data = this;
      //Parse files[0] to json
      Papa.parse(files[0], {
        header: true,
        complete: function(results) {
          console.log(results.data);
          //set json data to categoryList
          _data.categoryList = results.data;
        }
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
