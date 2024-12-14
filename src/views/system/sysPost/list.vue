<template>
  <div class="app-container">
    <div class="search-div">
      <el-form label-width="70px" size="small">
        <el-row>
          <el-col :span="12">
            <el-form-item label="岗位名称">
              <el-input v-model="searchObj.name" placeholder="岗位名称" clearable />
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
      <el-button type="success" icon="el-icon-plus" :disabled="$hasBP('bnt.sysPost.add') === false" size="mini" @click="add">添 加</el-button>
      <el-button class="btn-add" size="mini" :disabled="$hasBP('bnt.sysPost.remove') === false" @click="batchRemove()">批量删除</el-button>
    </div>
    <!-- 列表 -->
    <el-table
      v-loading="listLoading"
      :data="list"
      :row-key="getRowKeys"
      stripe
      border
      style="width: 100%;margin-top: 10px;"
      @selection-change="handleSelectionChange"
    >
      <el-table-column type="selection" :reserve-selection="true" />
      <el-table-column
        label="序号"
        width="70"
        align="center"
      >
        <template slot-scope="scope">
          {{ (page - 1) * limit + scope.$index + 1 }}
        </template>
      </el-table-column>

      <el-table-column sortable prop="postCode" label="岗位编码" />
      <el-table-column sortable prop="name" label="岗位名称" />
      <el-table-column sortable prop="description" label="描述" />
      <el-table-column sortable prop="createTime" label="创建时间" />
      <el-table-column sortable prop="updateTime" label="修改时间" />
      <el-table-column label="操作" align="center" fixed="right">
        <template slot-scope="scope">
          <el-button type="primary" :disabled="$hasBP('bnt.sysPost.update') === false" icon="el-icon-edit" size="mini" title="修改" @click="edit(scope.row.id)" />
          <el-button
            type="danger"
            :disabled="$hasBP('bnt.sysPost.remove') === false"
            icon="el-icon-delete"
            size="mini"
            title="删除"
            @click="removeDataById(scope.row.id)"
          />
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
      <el-form
        ref="dataForm"
        :model="sysPost"
        :rules="rules"
        label-width="100px"
        size="small"
        style="padding-right: 40px;"
      >
        <el-form-item label="岗位编码" prop="postCode">
          <el-input v-model="sysPost.postCode" clearable />
        </el-form-item>
        <el-form-item label="岗位名称" prop="name">
          <el-input v-model="sysPost.name" clearable />
        </el-form-item>
        <el-form-item label="描述" prop="description">
          <el-input v-model="sysPost.description" clearable />
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" icon="el-icon-refresh-right" @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" icon="el-icon-check" size="small" @click="saveOrUpdate()">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
import api from '@/api/system/sysPost'
const defaultForm = {
}
export default {
  components: {
  },
  data() {
    return {
      listLoading: true, // 数据是否正在加载
      list: [], // banner列表
      total: 0, // 数据库中的总记录数
      page: 1, // 默认页码
      limit: 5, // 每页记录数
      searchObj: {}, // 查询表单对象
      dialogVisible: false,
      sysPost: defaultForm,
      saveBtnDisabled: false,
      selectValue: [], // 复选框选择内容封装数组
      rules: {

        postCode:
          [
            { required: true, message: '请输入岗位编码', trigger: 'blur' }
          ],
        name:
          [
            { required: true, message: '请输入岗位名称', trigger: 'blur' }
          ],
        description:
          [
            { required: true, message: '请输入描述', trigger: 'blur' }
          ],
        status:
          [
            { required: true, message: '请输入状态（1正常 0停用）', trigger: 'blur' }
          ]
      }
    }
  },
  // 生命周期函数：内存准备完毕，页面尚未渲染
  created() {
    console.log('list created......')
    this.fetchData()
  },
  // 生命周期函数：内存准备完毕，页面渲染成功
  mounted() {
    console.log('list mounted......')
  },
  methods: {
    getRowKeys(row) {
      return row.id
    },
    // 每页数
    sizeChangeHandle(val) {
      this.limit = val
      this.page = 1
      this.fetchData(this.page)
    },
    // 加载banner列表数据
    fetchData(page = 1) {
      debugger
      this.page = page
      api.getPageList(this.page, this.limit, this.searchObj).then(
        response => {
          this.list = response.data.records
          this.total = response.data.total
          // 数据加载并绑定成功
          this.listLoading = false
        }
      )
    },
    // 重置查询表单
    resetData() {
      console.log('重置查询表单')
      this.searchObj = {}
      this.fetchData()
    },
    // 根据id删除数据
    removeDataById(id) {
      // debugger
      this.$confirm('此操作将永久删除该记录, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => { // promise
        // 点击确定，远程调用ajax
        return api.removeById(id)
      }).then((response) => {
        this.fetchData(this.page)
        this.$message.success(response.message || '删除成功')
      }).catch(() => {
        this.$message.info('取消删除')
      })
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
      this.$confirm('此操作将永久删除, 是否继续?', '提示', {
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
    // 弹出添加表单
    add() {
      this.dialogVisible = true
      this.sysPost = {
      }
      this.rules.postCode = [{ required: true, message: '请输入岗位编码', trigger: 'blur' }]
      this.rules.name = [{ required: true, message: '请输入岗位名称', trigger: 'blur' }]
      this.rules.description = [{ required: true, message: '请输入描述', trigger: 'blur' }]
      this.rules.status = [{ required: true, message: '请输入状态（1正常 0停用）', trigger: 'blur' }]
    },
    // 编辑
    edit(id) {
      this.dialogVisible = true

      this.rules.postCode = [{ required: true, message: '请输入岗位编码', trigger: 'blur' }]
      this.rules.name = [{ required: true, message: '请输入岗位名称', trigger: 'blur' }]
      this.rules.description = [{ required: true, message: '请输入描述', trigger: 'blur' }]
      this.rules.status = [{ required: true, message: '请输入状态（1正常 0停用）', trigger: 'blur' }]
      //
      // 有问题
      api.getById(id).then(response => {
        this.sysPost = response.data
      })
    },
    // 添加或更新
    saveOrUpdate() {
      this.$refs.dataForm.validate(valid => {
        if (valid) {
          if (!this.sysPost.id) {
            this.saveRole()
          } else {
            this.updateRole()
          }
        }
      })
    },
    // 添加
    saveRole() {
      api.saveRole(this.sysPost).then(response => {
        this.$message.success('操作成功')
        this.dialogVisible = false
        this.fetchData(this.page)
      })
    },
    // 更新
    updateRole() {
      api.updateRole(this.sysPost).then(response => {
        this.$message.success(response.message || '操作成功')
        this.dialogVisible = false
        this.fetchData(this.page)
      })
    }
  }
}
</script>
<style>
</style>
