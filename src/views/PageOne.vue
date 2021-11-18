<template>
  <div class="div-style">
    <el-button :plain="true" type="primary" @click="runSript() ">执行</el-button>
    <el-button :plain="true" type="primary" @click="page(1) ">刷新</el-button>
    <br></br>
    <el-table
        :data="tableData"
        border
        style="width: 100%">
      <el-table-column
          fixed
          prop="sqlFileName"
          label="文件名"
          width="150">
      </el-table-column>
      <el-table-column
          prop="sqlFilePath"
          label="文件路径"
          width="500">
      </el-table-column>
      <el-table-column
          prop="sqlFileCharset"
          label="文件编码"
          width="120">
      </el-table-column>
      <el-table-column
          prop="sqlFileExecuteFlag"
          label="文件执行状态"
          width="120">
      </el-table-column>
      <el-table-column
          prop="sqlFileExecuteLog"
          label="文件日志"
          width="300">
      </el-table-column>
      <el-table-column
          fixed="right"
          label="操作"
          width="100">
        <template slot-scope="scope">
          <el-button @click="handleClick(scope.row)" type="text" size="small">查看</el-button>
          <el-button type="text" size="small" @click="updateData(scope.row)">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
        background
        layout="prev, pager, next"
        :page-size="pageSize"
        :total="total"
        @current-change="page">
    </el-pagination>
  </div>
</template>

<script>
import {Message} from "element-ui";

let curPag = 1;
export default {
  data() {
    return {
      pageSize:null,
      total:null,
      tableData:null
    }
  },
  methods: {
    runSript() {
      axios.post('http://localhost:8081/stock/runScript').then(function (resp) {
        let result = resp.data;
        if(result.data === 1){ //成功
          Message.success("数据加载任务已交由后端执行！");
          this.page(1);
        }else{
          Message.warning('数据加载任务已交由后端执行，请勿重复提交执行！')
        }
      })
    },
    handleClick(row) {
      console.log(row);
    },
    page(currentPage){
      const _this=this;
      axios.get('http://localhost:8081/stock/findAll/'+(currentPage-1)+'/10').then(function (resp) {
        _this.tableData=resp.data.content;
        _this.pageSize=resp.data.size;
        _this.total=resp.data.totalElements;
      })
    },
    pageNew(currentPage){
      console.log("当前页数为："+currentPage);
      const _this=this;
      axios.get('http://localhost:8081/stock/findAll/'+(currentPage-1)+'/10').then(function (resp) {
        _this.tableData=resp.data.content;
        _this.pageSize=resp.data.size;
        _this.total=resp.data.totalElements;

      });
    },
    times(){
      return setInterval(()=>{
        this.pageNew(curPag)
        curPag++;
      },10000)
    },
    updateData(row){
      this.$router.push({
        path: '/update',
        query: {
          id: row.serialid
        }
      });
    }
  },
  created() {
    const _this = this;
    axios.get('http://localhost:8081/stock/findAll/0/100').then(function (resp) {
      _this.tableData = resp.data.content;
      _this.pageSize = resp.data.size;
      _this.total = resp.data.totalElements;
    });
    //直接启动定时器刷新数据
    //this.times();
  }
}
</script>
<style>

.div-style {
  height: 100%;
}
</style>
