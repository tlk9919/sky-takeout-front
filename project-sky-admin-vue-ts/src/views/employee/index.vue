<template>
  <div class="dashboard-container">
    <div class="container">
      <div class="tableBar">
        <label class="label" style="margin-right: 5px">员工姓名：</label>
        <el-input v-model="name" placeholder="请输入员工姓名" style="width: 15%"/>
        <el-button type="primary" style="margin-left: 5px" @click="pageQuery">查询</el-button>
        <el-button type="primary" style="float: right">+添加员工</el-button>
      </div>
      <el-table
        :data="records"
        stripe
        style="width: 100%">
        <el-table-column
          prop="name"
          label="员工姓名"
          width="180">
        </el-table-column>
        <el-table-column
          prop="username"
          label="账号"
          width="180">
        </el-table-column>
        <el-table-column
          prop="phone"
          label="手机号">
        </el-table-column>
        <el-table-column
          prop="status"
          label="账号状态">
          <template slot-scope="scope">
            <span v-if="scope.row.status == 1">启用</span>
            <span v-else>禁用</span>
          </template>
        </el-table-column>
        <el-table-column
          prop="updateTime"
          label="最后操作时间">
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button type="text">修改</el-button>
            <el-button type="text" @click="handleStartOrStop(scope.row)">{{scope.row.status == 1 ? '禁用' : '启用'}}</el-button>
          </template>
        </el-table-column>
      </el-table>
      <div class="block">
        <el-pagination
          class="pageList"
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="page"
          :page-sizes="[10, 20, 30, 40,50]"
          :page-size="pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total">
        </el-pagination>
      </div>
    </div>
    </div>

</template>
<script lang="ts">
import {getEmployeeList,enableOrDisableEmployee} from '@/api/employee'

export default  {
  data() {
    return {
      name: '', //员工姓名
      page: 1, // 当前页码
      pageSize: 10,// 每页显示条数
      total: 0,// 总记录数
      records: []// 当前页要展示的数据集合
    }
  },
  created() {
    this.pageQuery()
  },
methods: {
  //分页查询
  pageQuery() {
    const params = {
      name: this.name,
      page: this.page,
      pageSize: this.pageSize
    }
    //发送ajax请求，访问后端服务，获取分页数据
    getEmployeeList(params).then((response) => {
      if (response.data.code == 1) {
        this.records = response.data.data.records
        this.total = response.data.data.total
      }
}).catch((error) => {
  this.$message.error('请求出错了',error.message)
    })
  },
  // 每页显示条数改变，即pageSize改变触发
  handleSizeChange(pageSize){
    this.pageSize = pageSize
    this.pageQuery()
  },
  //page 当前页码改变触发
  handleCurrentChange(page){
    this.page = page
    this.pageQuery()
  }, // 禁用或启用
  handleStartOrStop(row){
    if (row.username == 'admin') {
      this.$message.warning('系统管理员，，不能更改状态')
      return
    }
    this.$confirm('确认要修改当前员工账号状态吗, 是否继续?', '提示', {
      confirmButtonText: '确定',
      cancelButtonText: '取消',
      type: 'warning'
    }).then(() => {
      const p = {
        id: row.id,
        status: row.status == 1 ? 0 : 1
      }
      enableOrDisableEmployee(p).then((response) => {
        if (response.data.code == 1) {
          this.$message.success('操作成功')
          this.pageQuery()
        }
      })
    })
  }
}
}
</script>

<style lang="scss" scoped>
.disabled-text {
  color: #bac0cd !important;
}
</style>
