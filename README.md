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
  
   2 .script  模版

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


```
user: 更改用户状态
role: 1. 根据用户id查询用户分配的角色
      2. 分配角色

```


### 第四次提交：
关联：后端第五次提交

不同角色的用户登录后台管理系统拥有不同的菜单权限与功能权限。
前端是基于模块开发的，菜单表设计也必须基于前端模板进行设计


##### 菜单管理

1. 配置路由（src/router/index.js）
```
{
        name: 'sysMenu',
        path: 'sysMenu',
        component: () => import('@/views/system/sysMenu/list'),
        meta: {
          title: '菜单管理',
          icon: 'el-icon-s-unfold'
        }
      },
```
2. 创建 src/api/system/sysMenu.js
3. 在src/views/system/sysMenu/list.vue里添加内容

说明：库存管理大项 和 系统管理里的字典管理在数据库里隐藏了。到时候可能要再手动显示出来

```
问题：删除时报错 TypeError: _vm is not a function

list里面调用删除接口的方法的名字，不能和删除接口的名字相同
  
  removeDataById(id) {
      }).then(() => { // promise
        // 点击确定，远程调用ajax
        return api.removeById(id)
      }).then((response) => {
      }).catch(() => {
        this.$message.info('取消删除')
      })
    },
```

给list.vue 添加了一个switchStatus 方法。

##### 给角色分配权限

1. 配置路由（src/router/index.js）
```
{
        path: 'assignAuth',
        component: () => import('@/views/system/sysRole/assignAuth'),
        meta: {
          activeMenu: '/system/sysRole',
          title: '角色授权'
        },
        hidden: true
      }
```

2. 在src/api/system/sysMenu.js里添加 【查看某个角色的权限列表】 和【给某个角色授权】方法
```
  /*
  查看某个角色的权限列表
  */
  toAssign(roleId) {
    return request({
      url: `${api_name}/toAssign/${roleId}`,
      method: 'get'
    })
  },
  
    /*
  给某个角色授权
  */
  doAssign(assginMenuVo) {
    return request({
      url: `${api_name}/doAssign`,
      method: "post",
      data: assginMenuVo
    })
  }
```

3. 在  src/views/system/sysRole/list.vue 中，添加 按钮及方法

```
<el-button type="warning" icon="el-icon-baseball" size="mini" @click="showAssignAuth(scope.row)" title="分配权限"/>
```
```
 // 跳转分配菜单权限
    showAssignAuth(row) {
      this.$router.push('/assignAuth?id=' + row.id + '&roleName=' + row.roleName);
    },
```

4. 在 src/views/system/sysRole/assignAuth中编写代码
```
报错：url 找不到：404
把router里的404上面那个删掉

再报错404：把showAssignAuth 里面的路径改成：/system/assignAuth?id= 

再再报错404 ： 重启后端
```


### 第五次提交：
关联：后端第六次提交

内容：通用权限系统，前端权限对接

**1、修改request.js文件**
改成    
```
config.headers['token'] = getToken()
```

要从后端的请起头里获得token的值

**2、store/modules/user.js**
新增菜单及按钮处理
```
    buttons: [], // 新增
    menus: '' //新增
```
```

  // 新增按钮
  SET_BUTTONS: (state, buttons) => {
    state.buttons = buttons
  },
  // 新增菜单
  SET_MENUS: (state, menus) => {
    state.menus = menus
  }
```

```
  // get user info
  getInfo({ commit, state }) {
    return new Promise((resolve, reject) => {
      getInfo(state.token).then(response => {
        const { data } = response

        if (!data) {
          return reject('Verification failed, please Login again.')
        }

        const { name, avatar } = data

        commit('SET_NAME', name)
        commit('SET_AVATAR', avatar)

        commit("SET_BUTTONS", data.buttons)
        commit("SET_MENUS", data.routers)
        resolve(data)
      }).catch(error => {
        reject(error)
      })
    })
  },
```
**3、store/getters.js**
```
 //新增
  buttons: state => state.user.buttons,
  menus: state => state.user.menus
```

**4、src/router**

先在router这个目录下新建两个js文件，开发环境和生产环境导入组件的方式略有不同

- _import_production.js


- _import_development.js

**5、src/permission.js**
整体替换该文件
```
import router from './router'
import store from './store'
import { getToken } from '@/utils/auth'
import { Message } from 'element-ui'
import NProgress from 'nprogress' // 水平进度条提示: 在跳转路由时使用
import 'nprogress/nprogress.css' // 水平进度条样式
import getPageTitle from '@/utils/get-page-title' // 获取应用头部标题的函数


import Layout from '@/layout'
import ParentView from '@/components/ParentView'
const _import = require('./router/_import_'+process.env.NODE_ENV) // 获取组件的方法

NProgress.configure({ showSpinner: false }) // NProgress Configuration
const whiteList = ['/login'] // no redirect whitelist



router.beforeEach(async(to, from, next) => {
  NProgress.start()
// set page title
  document.title = getPageTitle(to.meta.title)
// determine whether the user has logged in
  const hasToken = getToken()
  if (hasToken) {
    if (to.path === '/login') {
      // if is logged in, redirect to the home page
      next({ path: '/' })
      NProgress.done()
    } else {
      const hasGetUserInfo = store.getters.name
      if (hasGetUserInfo) {
        next()
      } else {
        try {
          // get user info
          await store.dispatch('user/getInfo')// 请求获取用户信息
          if (store.getters.menus.length < 1) {
            global.antRouter = []
            next()
          }
          const menus = filterAsyncRouter(store.getters.menus)// 1.过滤路由
          //console.log('menus='+JSON.parse(menus))
          router.addRoutes(menus) // 2.动态添加路由
          let lastRou = [{ path: '*', redirect: '/404', hidden: true }]
          router.addRoutes(lastRou)
          global.antRouter = menus // 3.将路由数据传递给全局变量，做侧边栏菜单渲染工作
          next({
            ...to,
            replace: true
          })
          //next()
        } catch (error) {
          // remove token and go to login page to re-login
          console.log(error)
          await store.dispatch('user/resetToken')
          Message.error(error || 'Has Error')
          next(`/login?redirect=${to.path}`)
          NProgress.done()
        }
      }
    }
  } else { /* has no token*/
    if (whiteList.indexOf(to.path) !== -1) {
      // in the free login whitelist, go directly
      next()
    } else {
      // other pages that do not have permission to access are redirected to the login page.
      next(`/login?redirect=${to.path}`)
      NProgress.done()
    }
  }
})

router.afterEach(() => { // finish progress bar
  NProgress.done()
}) // // 遍历后台传来的路由字符串，转换为组件对象
function filterAsyncRouter(asyncRouterMap) {
  const accessedRouters = asyncRouterMap.filter(route => {

    if (route.component) {
      console.log('route.component='+route.component);
      if (route.component === 'Layout') {
        route.component = Layout
      }else if (route.component === 'ParentView') {
        route.component = ParentView
      }else {
        try {
          route.component = _import(route.component)// 导入组件
        } catch (error) {
          debugger
          console.log(error)
          route.component = _import('dashboard/index')// 导入组件
        }
      }
    }
    if (route.children && route.children.length > 0) {
      route.children = filterAsyncRouter(route.children)
    } else {
      delete route.children
    }
    return true
  })
  return accessedRouters
}

```

**6、src/router**
删除index.js中自定义的路由

- 其他：
动态路由：后台传递路由，前端拿到并生成侧边栏
参考：https://segmentfault.com/a/1190000010043013
删掉这个路由是为了动态地用路由，不然菜单写了就没用了

- 其他：
如果一个用户拥有多个角色，如何分配权限？
白名单：取并集
黑名单：取交集

**7.src/components**

在scr/components目录下新建ParentView文件夹，添加index.vue


**8、layout/components/SideBar/index.vue**

```
return this.$router.options.routes.concat(global.antRouter)
 ```

**9、utils/btn-permission.js**
在uitls目录添加btn-permission.js文件

**10、main.js**
```
//新增
import hasBtnPermission from '@/utils/btn-permission'
Vue.prototype.$hasBP = hasBtnPermission

```

```
报错：Module not found: Error: Can't resolve '@/iconfont/iconfont.css

导入iconfront包
```


**11、按钮权限控制**
$hasBP(‘bnt.sysRole.add’)控制按钮是否显示
```
<el-button type="success" icon="el-icon-plus" size="mini" @click="add" :disabled="$hasBP('bnt.sysRole.add')  === false">添 加</el-button>
```

~~※※※※※※※※※※※※※ 表sys_menu删了一行。问题是我也不知道删了哪一行。以后遇到bug再说吧~~


**12、修改 login/index.vue里的密码校验规则**
```
const validateUsername = (rule, value, callback) => {
      if (!value.length < 5) {
        callback(new Error('Please enter the correct user name'))
      } else {
        callback()
      }
    }
    const validatePassword = (rule, value, callback) => {
      if (value.length < 6) {
        callback(new Error('The password can not be less than 6 digits'))
      } else {
        callback()
      }
    }
```


13. 权限测试方式
一、使用超级管理员登录
（1）创建新用户
（2）为新用户分配角色
（3）为角色分配菜单和按钮权限
    ```
     问题：
     为角色分配权限时溢出
     把 system/sysRole/list.vue里 修改为
    
    
        //跳转分配菜单权限
            showAssignAuth(row) {
                this.$router.push('/assignAuth?id=' + row.id + '&roleName=' + row.roleName);
            },
     ```

二、使用新创建用户进行登录

 (1)进行查看

- 其他
路径 ``` http://192.168.3.227:9528/#/login?redirect=%2FsysMenu ```
假如管理员有这个权限，而用户没有这个权限的话，那么可能会出现用户登录不上来的情况。
解决方法：把后面的重定向删除，重新登陆。

权限测试没有问题。均正常。



_7.测试服务权限_
(关联后端)
登录后台，分配权限进行测试，页面如果添加了按钮权限控制，可临时去除方便测试

1. 使用管理员先登录
2. 给用户分配角色管理的权限（无删除权限）
3. 使用用户角色登录
4. 测试权限，发现未分配的删除权限无法操作

无问题


### 第六次提交：（关联：后端第八次）

第一步：在前端显示出页面

把按照之前的知识，做出 部门管理 和 岗位管理 接口
- 部门管理
创建 src/views/system/sysDept
在下面编写list.vue
部门管理是仿照菜单管理进行的。

- 岗位管理
创建 src/views/system/sysPost
在下面编写list.vue
岗位管理是仿照角色管理进行的。




