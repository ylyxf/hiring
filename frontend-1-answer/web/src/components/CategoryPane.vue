<template>
  <div>
    <FileButton iconCls="icon-save" @select="importCategory">导入分类</FileButton>
    <DataGrid idField="id" :data="categoryList" style="height:250px">
      <GridColumn field="name" title="Name"></GridColumn>
      <GridColumn field="typeName" title="Type"></GridColumn>
    </DataGrid>
  </div>
</template>

<script>
import Papa from "papaparse";

export default {
  data() {
    return {
      categories: []
    };
  },
  computed: {
    categoryList() {
      //referebced in your template just as booksList
      return this.categories.map(category => {
        category.typeName = category.type == 0 ? "支出" : "收入";
        return category;
      });
    }
  },
  methods: {
    importCategory: function(files) {
      // this can't be used in callback functions,defind _data to hold this
      let _data = this;
      //Parse files[0] to json
      Papa.parse(files[0], {
        header: true,
        complete: function(results) {
          console.log(results.data);
          //set json data to categoryList
          _data.categories = results.data;
        }
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
