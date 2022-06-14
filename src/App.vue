<template>
  <div id="app">
    <el-card class="box-card">
      <el-tabs v-model="activeName" :before-leave="handleBeforeLeave">
        <el-tab-pane
          v-for="tab in tabs"
          :key="tab.key"
          :label="tab.name"
          :name="tab.key"
        />
      </el-tabs>
      <el-table :data="dataList[activeName]" style="width: 100%">
        <el-table-column
          v-for="column in columns"
          :key="column.prop"
          :prop="column.prop"
          :label="column.label"
        />
        <el-table-column label="操作" width="100">
          <template slot-scope="scope">
            <el-button
              @click="handleRemove(scope.$index)"
              type="danger"
              size="mini"
              >删除</el-button
            >
          </template>
        </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script>
import tabs from "./config/tabs";
import columns from "./config/columns";
import dataList from "./config/dataList";
export default {
  name: "App",
  data() {
    return {
      tabs,
      columns,
      dataList,
      isRemove: false,
      activeName: "tab1",
    };
  },
  methods: {
    // 这个地方是一个坑点 按照普通的逻辑是无法正确的阻止切换
    // 但是这个地方应该是组件库的 BUG
    async handleBeforeLeave() {
      if (this.isRemove) {
        await this.$confirm(
          "上一个标签的表格数据有删除, 是否继续切换?",
          "提示",
          {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
          }
        );
        this.isRemove = false;
      }
      // 这个地方的 return false 并不会阻止 tabs 的切换
      // if (this.isRemove) {
      //   this.$confirm("上一个标签的表格数据有删除, 是否继续切换?", "提示", {
      //     confirmButtonText: "确定",
      //     cancelButtonText: "取消",
      //     type: "warning",
      //   })
      //     .then(() => {
      //       console.log("切换")
      //     })
      //     .catch(() => {
      //       console.log("取消")
      //       return false;
      //     });
      // }
    },
    handleRemove(index) {
      this.$confirm("此操作将删除该条数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.dataList[this.activeName].splice(index, 1);
          this.isRemove = true;
          this.$message({
            duration: 1000,
            type: "success",
            message: "删除成功!",
          });
        })
        .catch(() => {
          this.$message({
            duration: 1000,
            type: "info",
            message: "已取消删除",
          });
        });
    },
  },
};
</script>

<style scoped>
#app {
  width: 500px;
  margin: 0 auto;
}
</style>