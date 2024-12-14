<template>
  <div class="app-container">
    <!--查询表单-->
    <div class="search-div">
      <el-form label-width="70px" size="small">
        <el-row>
          <el-col :span="24">
            <el-form-item label="角色名称">
              <el-input v-model="searchObj.roleName" style="width: 100%" placeholder="角色名称" />
            </el-form-item>
          </el-col>
        </el-row>
        <el-row style="display:flex">
          <el-button type="primary" icon="el-icon-search" size="mini" @click="fetchData()">搜索</el-button>
          <el-button icon="el-icon-refresh" size="mini" @click="resetData">重置</el-button>
        </el-row>
      </el-form>
    </div>

    <!-- 工具条 -->
    <div class="tools-div">
      <el-button type="success" icon="el-icon-plus" size="mini" @click="add">添 加</el-button>
      <el-button class="btn-add" size="mini" @click="batchRemove()">批量删除</el-button>
    </div>

    <!-- 表格 -->
    <el-table
      v-loading="listLoading"
      :data="list"
      stripe
      border
      style="width: 100%;margin-top: 10px;"
      @selection-change="handleSelectionChange"
    >
      <!--    第一列复选框-->
      <el-table-column type="selection" />

      <el-table-column
        label="序号"
        width="70"
        align="center"
      >
        <template slot-scope="scope">
          {{ (page - 1) * limit + scope.$index + 1 }}
        </template>
      </el-table-column>

      <el-table-column prop="roleName" label="角色名称" />
      <el-table-column prop="roleCode" label="角色编码" />
      <el-table-column prop="createTime" label="创建时间" width="160" />
      <el-table-column label="操作" width="200" align="center">
        <template slot-scope="scope">
          <el-button type="primary" icon="el-icon-edit" size="mini" title="修改" @click="edit(scope.row.id)" />
          <el-button type="danger" icon="el-icon-delete" size="mini" title="删除" @click="removeDataById(scope.row.id)" />
          <el-button type="warning" icon="el-icon-baseball" size="mini" title="分配权限" @click="showAssignAuth(scope.row)" />
        </template>
      </el-table-column>
    </el-table>

    <!-- 分页组件 -->
    <el-pagination
      :current-page="page"
      :total="total"
      :page-size="limit"
      style="padding: 30px 0; text-align: center;"
      layout="total, prev, pager, next, jumper"
      @current-change="fetchData"
    />

    <el-dialog title="添加/修改" :visible.sync="dialogVisible" width="40%">
      <el-form ref="dataForm" :model="sysRole" label-width="150px" size="small" style="padding-right: 40px;">
        <el-form-item label="角色名称">
          <el-input v-model="sysRole.roleName" />
        </el-form-item>
        <el-form-item label="角色编码">
          <el-input v-model="sysRole.roleCode" />
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" icon="el-icon-refresh-right" @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" icon="el-icon-check" size="small" @click="saveOrUpdate()">确 定</el-button>
      </span>
    </el-dialog>

  </div>

</template>

<script setup>
// 1.引入定义接口的js文件
import api from '@/api/system/sysRole'
// eslint-disable-next-line no-unused-vars
import sysRole from '@/api/system/sysRole'
export default {
// 定义数据模型/初始值
  data() {
    return {
      listLoading: true, // 数据是否正在加载
      list: [], // 角色列表
      total: 0, // 总记录数
      page: 1, // 页码
      limit: 5, // 每页记录数
      searchObj: {}, // 查询条件

      dialogVisible: false, // 弹出框
      sysRole: {}, // 封装添加表单数据
      selectValue: [], // 复选框选择内容封装数组
      rules: {
        roleName: [{ required: true, message: '请输入角色名称', trigger: 'blur' }],
        roleCode: [{ required: true, message: '请输入角色编码', trigger: 'blur' }]
      }
    }
  },
  // 页面渲染之前获取数据
  created() {
    // 调用列表方法
    this.fetchData()
  },
  // 定义（具体）方法
  methods: {
  // 跳转分配菜单权限
    showAssignAuth(row) {
      // this.$router.push('/system/assignAuth?id=' + row.id + '&roleName=' + row.roleName)
      this.$router.push('/assignAuth?id=' + row.id + '&roleName=' + row.roleName)
    },
    // 复选框发生变化执行方法
    handleSelectionChange(selection) {
      this.selectValue = selection
      // console.log(this.selectValue)
    },

    // 批量删除
    batchRemove() {
      // 判断
      // eslint-disable-next-line eqeqeq
      if (this.selectValue.length == 0) {
        this.$message.warning('请选择要删除的记录！')
        return
      }
      this.$confirm('此操作将永久删除该角色, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 数组
        var idList = []
        // 获取多个复选框对应id，封装到数组里面
        // [1,2,3]
        for (var i = 0; i < this.selectValue.length; i++) {
          var obj = this.selectValue[i]
          // id值
          var id = obj.id
          // 放到数组里面
          idList.push(id)
        }
        api.batchRemove(idList).then(response => {
          // 提示
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
          // 刷新
          this.fetchData()
        })
      })
    },

    // 删除
    removeDataById(id) {
      this.$confirm('此操作将永久删除该角色, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 调用方法删除
        api.removeById(id)
          .then(response => {
            // 提示
            this.$message({
              type: 'success',
              message: '删除成功!'
            })
            // 刷新
            this.fetchData()
          })
      })
    },

    // 修改-数据回显
    edit(id) {
      // 弹出框
      this.dialogVisible = true
      api.getRoleId(id).then(response => {
        this.sysRole = response.data
      })
    },
    // 点击确定
    saveOrUpdate() {
      // 判断添加还是修改
      this.$refs.dataForm.validate((valid) => {
        if (valid) {
          if (!this.sysRole.id) {
            // 添加
            this.saveRole()
          } else {
            this.updateRole()
          }
        }
      })
    },
    // 修改的方法
    updateRole() {
      api.update(this.sysRole)
        .then(response => {
          // 提示
          this.$message({
            type: 'success',
            message: '修改成功!'
          })
          // 关闭弹框
          this.dialogVisible = false
          // 刷新页面
          this.fetchData()
        })
    },
    // 添加的方法
    saveRole() {
      api.saveRole(this.sysRole)
        .then(response => {
          // 提示
          this.$message({
            type: 'success',
            message: '添加成功!'
          })
          // 关闭弹框
          this.dialogVisible = false
          // 刷新页面
          this.fetchData()
        })
    },
    // 点击添加，弹出框
    add() {
      this.dialogVisible = true
      this.sysRole = {}
    },

    // 重置表单
    resetData() {
      console.log('重置查询表单')
      this.searchObj = {}
      this.fetchData()
    },
    // 条件分页查询列表
    // pageNum 查询页数
    fetchData(pageNum = 1) {
      // 页数赋值
      this.page = pageNum
      // 调用api
      api.getPageList(this.page, this.limit, this.searchObj).then(response => { // response:返回的信息
        /*
        * {
    "code": 200,
    "message": "成功",
    "data": {
        "records": [
            {
                "id": "1863191249775312898",
                "createTime": "2024-12-01T11:59:45.000+0000",
                "updateTime": "2024-12-01T11:59:45.000+0000",
                "isDeleted": 0,
                "param": {},
                "roleName": "aaaaaa",
                "roleCode": "aaaaaa",
                "description": "aaaaaaaaa"
            },
            {
                "id": "1863096210889957378",
                "createTime": "2024-12-01T05:42:06.000+0000",
                "updateTime": "2024-12-01T05:42:06.000+0000",
                "isDeleted": 0,
                "param": {},
                "roleName": "添加添加添加添加添加",
                "roleCode": "role",
                "description": "BaseAuthParentServiceApplicationTests"
            }
        ],
        "total": 17,
        "size": 2,
        "current": 1,
        "orders": [],
        "optimizeCountSql": true,
        "hitCount": false,
        "countId": null,
        "maxLimit": null,
        "searchCount": true,
        "pages": 9
    }
}
*/
        // console.log(response)

        debugger
        this.listLoading = false
        this.list = response.data.records // 每页数据列表
        this.total = response.data.total // 总记录数
      })
    }
  }

}

</script>

<style scoped lang="scss">

</style>
