<template>
  <div class="dashboard-container">
    <div class="container">
      <div class="tableBar">
          <label class="label" style="margin-right: 5px">套餐名称：</label>
          <el-input v-model="name" placeholder="请输入套餐名称" style="width: 15%"/>
        <label class="label" style="margin-right: 5px">套餐分类：</label>
        <el-select v-model="categoryId" placeholder="套餐分类">
          <el-option
            v-for="item in options"
            :key="item.id"
            :label="item.name"
            :value="item.id">
          </el-option>
        </el-select>
        <label class="label" style="margin-right: 5px">售卖状态：</label>
        <el-select v-model="status" placeholder="售卖状态">
          <el-option
            v-for="item in statusArr"
            :key="item.id"
            :label="item.name"
            :value="item.id">
          </el-option>
        </el-select>
          <el-button type="primary" style="margin-left: 5px" @click="pageQuery">查询</el-button>
        <div style="float: right">
          <el-button type="info">+新增套餐</el-button>
          <el-button type="danger" >批量删除</el-button>
        </div>
        </div>
      <el-table :data="records" stripe class="tableBox" @selection-change="handleSelectionChange">
        <el-table-column type="selection" width="25" />
        <el-table-column prop="name" label="套餐名称" />
        <el-table-column label="图片">
          <template slot-scope="scope">
            <el-image style="width: 80px; height: 40px; border: none" :src="scope.row.image"></el-image>
          </template>
        </el-table-column>
        <el-table-column prop="categoryName" label="套餐分类" />
        <el-table-column prop="price" label="套餐价"/>
        <el-table-column label="售卖状态">
          <template slot-scope="scope">
            <div class="tableColumn-status" :class="{ 'stop-use': scope.row.status === 0 }">
              {{ scope.row.status === 0 ? '停售' : '启售' }}
            </div>
          </template>
        </el-table-column>
        <el-table-column prop="updateTime" label="最后操作时间" />
        <el-table-column label="操作" align="center" width="250px">
          <template slot-scope="scope">
            <el-button type="text" size="small"> 修改 </el-button>
            <el-button type="text" size="small" @click="handleStartOrStop(scope.row)">
              {{ scope.row.status == '1' ? '停售' : '启售' }}
            </el-button>
            <el-button type="text" size="small" @click="handleDelete('S',scope.row.id)"> 删除 </el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination class="pageList"
                     :page-sizes="[10, 20, 30, 40]"
                     :page-size="pageSize"
                     layout="total, sizes, prev, pager, next, jumper"
                     :total="total"
                     @size-change="handleSizeChange"
                     @current-change="handleCurrentChange" />
    </div>
  </div>
</template>

<script lang="ts">
import {getCategoryByType} from "@/api/category"
import {getSetmealPage} from "@/api/setMeal"
export default {
  data() {
    return {
      name: '', //套餐姓名
      page: 1, // 当前页码
      pageSize: 10,// 每页显示条数
      total: 0,// 总记录数
      records: [],// 当前页要展示的数据集合
      options:[],
      categoryId: '',
      statusArr:[
        {
          value:'0',
          label:''
        },
        {
          value:'1',
          label:''
        }
      ],
      status:''
    }
  },
  created() {
    getCategoryByType({
      type:'2'
    }).then(res=>{
      if (res.data.code === 1){
        this.options=res.data.data
      }
    })
    // 页面加载完后分页查询
      this.pageQuery()
  },
  methods: {
    //分页查询
    pageQuery(){
      //封装分页查询的参数
      const params = {
        name: this.name,
        page: this.page,
        pageSize: this.pageSize,
        categoryId: this.categoryId,
        status: this.status
      }
      getSetmealPage(params).then(res=>{
        if (res.data.code === 1){
          this.records = res.data.data.records
          this.total = res.data.data.total
        }
      })
    },
    handleSizeChange(pageSize){
      this.pageSize = pageSize
      this.pageQuery()
    },
    handleCurrentChange(page){
      this.page = page
      this.pageQuery()
    }
  }
}
</script>
<style lang="scss">
.el-table-column--selection .cell {
  padding-left: 10px;
}
</style>
<style lang="scss" scoped>
.dashboard {
  &-container {
    margin: 30px;

    .container {
      background: #fff;
      position: relative;
      z-index: 1;
      padding: 30px 28px;
      border-radius: 4px;

      .tableBar {
        margin-bottom: 20px;
        .tableLab {
          float: right;
          span {
            cursor: pointer;
            display: inline-block;
            font-size: 14px;
            padding: 0 20px;
            color: $gray-2;
          }
        }
      }

      .tableBox {
        width: 100%;
        border: 1px solid $gray-5;
        border-bottom: 0;
      }

      .pageList {
        text-align: center;
        margin-top: 30px;
      }
      //查询黑色按钮样式
      .normal-btn {
        background: #333333;
        color: white;
        margin-left: 20px;
      }
    }
  }
}
</style>
