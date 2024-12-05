参考文件:
https://blog.csdn.net/lbw18/category_12071837.html

参考视频：
https://www.bilibili.com/video/BV1ad4y1y7LU?spm_id_from=333.788.player.switch&vd_source=1304db5a0c1ae88e416aa5ada3065487&p=19


### 第一次提交：
添加README-knowledge.md文件。存放一些关于前端的知识。
1.Node.js简介  2. NPM简介  3.模块化开发（ES5） 4.模块化开发（ES6）

```
遇到的问题及其解决方法：

 -- 在提交时,遇到了 【hint: Updates were rejected because the remote contains work that you do】的问题
 
    查了一下说是远程仓库里包括本地项目里没有的文件。
    
    是在创建仓库的时候点击了“添加README文件的选项。删除后重新提交，完成”

```

### 第二次提交：
1.前端环境搭建（vue-admin-template）+前端框架目录结构
2.改造框架登录功能（前端/后端）

  - 在 src/views/login/index.vue 中修改登录页title
  - 在vue.config中注销掉mock接口配置，并配置代理转发请求到后端目标接口
  - 在src/utils/request.js中修改res.code
  - 在src/api/user.js中修改接口（关联：后端IndexController中的接口）

3. 删除 src/router/index.js 中的多余路由 并配置新的路由

```
  {
    path: '/system',
    component: Layout,
    meta: {
      title: '系统管理',
      icon: 'el-icon-s-tools'
    },
    alwaysShow: true,
    children: [
      {
        path: 'sysRole',
        // component: () => import('@/views/system/sysRole/list'),
        meta: {
          title: '角色管理',
          icon: 'el-icon-user-solid'
        }
      },
      {
        path: 'sysUser',
        name: 'SysUser',
        // component: () => import('@/views/system/sysUser/list'),
        meta: { title: '用户管理', icon: 'tree' }
      }
    ]
  },
```
并在src/views文件夹下创建文件夹和文件：
- system/sysRole - > list.vue
- system/sysUser - > list.vue


```
遇到的问题及其解决方法：

 --  在 vue.config 中，因为浏览器的跨域问题，所以要配置 changeOrigin: true, // 支持跨域
     后端接口是5240

```

4.在api里定义接口 
创建文件 src/api/system/sysRole.js 
关联的后端文件：system.controller.SysRoleController

sysRole.js 是 一个沟通前端和后端的介质，通过它把后端接口中的数据传递给前端。
例:后端的条件分页查询有三个参数。所以前端api里也要有三个参数

```
知识点：es6模版字符串

<html>
<head></head>

<body>
    <script>
        var s1 = 'a'
        //es6变量
        let s2 = 'b'

        //es6变量
        const s3 = 'c'

        //es6 模版字符串
        let str = `${s1}` + "#" +  `${s3}`
    </script>
</body>
</html>

```

5.页面调用接口
src/views/system/sysRole/list.vue

- 首先初始化vue组件
- 定义data
- 定义methods
- 表格渲染
- 分页组件
- 顶部查询表单
- 表单重置方法
```
<script setup>
//1.引入定义接口的js文件
import api from '@/api/system/sysRole'

export default {
// 定义数据模型/初始值
  data(){
    return{

    }
  },
//页面渲染之前获取数据
  created() {
    //调用列表方法
    this.fetchData()
  },
// 定义（具体）方法
  methods: {
    //列表
    fetchData() {
    }
  }

}

</script>

<template>
  <div class="app-container">
    角色列表
  </div>
</template>

<style scoped lang="scss">

</style>


```


------------------------------------

6. 角色删除
- 定义api（在src/api/system/sysRole.js中定义removeById）
- 定义methods（在src/views/system/sysRole/list.vue中使用Element-ui的MessageBox 弹框组件）

```
遇到的问题及其解决方法：

 --  在删除的时候可能点击删除按钮没有反应且报错：
    1. 在list.vue的methods里，调用方法删除时，调用的名称应该和sysRole.js里的接口名称相同
      （名字不同会报错）
    2.后端 SysRole 里面，roleName的类型如果是Long,范围是2^53 ~ -2^53.（13位数）
      而mybatis生成的id范围是15位数。位数大于Long类型，会出问题。
      所以将roleName类型改成String。
      
```

7.角色添加
- 定义api (在src/api/system/sysRole.js中定义saveRole(role) )
- 定义data
- 定义添加按钮（src/views/system/sysRole/list.vue：表格上面添加按钮）
- 在public目录的index.html页面中添加样式
- 定义弹出层（src/views/system/sysRole/list.vue），分页组件下面添加弹出层
- 实现功能（resetData,saveOrUpdate,saveRole）
- 
```
知识点：提交方式：


  //列表
  getPageList(page,limit,searchObj) {
    return request({
      //接口路径
      url: `${api_name}/${page}/${limit}`,
      method: 'get', //提交方式
      //参数
      params: searchObj
    })
  },
  
  //添加
  saveRole(role) {
    return request({
      //接口路径
      url: `${api_name}/save`,
      method: 'post', //提交方式
      //传递json格式
      data: role
    })
  }

 getPageList 后端的 接口是  @PathVariable
 saveRole 后端的 接口是  @RequestBody 
  
所以在提交数据的时候会不同

```

```
思路：
首先添加一个添加按钮，按钮里调用add()方法
编写add()方法，弹出一个对话框
el-dialog 是这个对话框的代码
有两个按钮。然后“取消”按钮是通过把dialogVisible设置为false，关闭这个按钮
在点击 “确定”按钮 时，调用 saveOrUpdate()方法，判断是添加还是修改（判断方法是根据有无id）
然后再编写相关的方法，调用不同的接口，进行相应的操作
添加：saveRole 修改：updateRole
接口位置在：src/api/system/sysRole.js

```

8.角色修改
和角色添加基本一样，注意要在sysRole.js
getRoleId 和 update(role)  两个方法
因为要先查询到然后再修改

- 在list里添加数据回显

```
如果在数据回显时出错：
检查前端调用接口是否和后端一致
```

9.批量修改
- 定义api（src/api/system/sysRole.js）
- 初始化组件（src/views/system/sysRole/list.vue） 
    在table组件上添加 批量删除 按钮( @click="batchRemove()")
    在table组件上添加复选框(@selection-change="handleSelectionChange")
- 实现功能
   data定义数据( selectValue: [], //复选框选择内容封装数组)
- 完善方法：
  复选框发生变化执行方法:handleSelectionChange

### 第三次提交：
在 src/api/system/sysUser.js 里，添加 api
然后在src/views/system/sysUser/list.vue里添加模板部分
  1.search-div部分
  2.<script>模版
  3.fetchData()列表方法 

添加功能：add()  / saveOrUpdate() / save() 
修改功能：update() / edit()
删除功能：removeDataById

**更改用户状态**
在src/api/system/sysUser.js里 添加 updateStatus 接口
在 src/views/system/sysUser/list.vue 中添加方法
```
// 切换用户状态
    switchStatus(row) {
      row.status = row.status === 1 ? 0 : 1
      api.updateStatus(row.id, row.status).then(response => {
        if (response.code) {
          this.$message.success(response.message || '操作成功')
          this.fetchData()
        }
      })
    },
```

```
问题：如果显示“成功”但滑块没有变：修改el-switch中的v-model

vue中v-model绑定三目运算符报错解决:

这是由于v-model不能使用表达式，根据需要绑定的参数使用计算属性
       v-model ="scope.row.status===1" 换成
        :value="scope.row.status===1"

```



**给用户分配角色**
1.在src/api/system/sysRole.js中添加api
2.修改页面:更改src/views/system/sysUser/list.vue
- 添加分配角色按钮
- 添加分配角色表单
- 添加方法

```
问题：如果没有显式具体的内容：
大概是因为把getRolesByUserId和assignRoles这两个应该写在sysRole.js里的内容卸载sysUser里了...
```