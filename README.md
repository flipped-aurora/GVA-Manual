# GVA-Manual（GVA小白式使用说明书）

## 可以先看一遍视频

- [【开源项目教学】gin-vue-admin 2.0目录介绍和自动化开发模式（1010工作室出品）](https://www.bilibili.com/video/av413011289)
- [【gin-vue-admin】06/07更新：UI美化，插件功能，自动化代码增加搜索条件 字段描述 结构体中文描述（1010工作室出品）](https://www.bilibili.com/video/av668469402)
- [gin-vue-admin-2020-6-13直播分享会](https://www.bilibili.com/video/av328557270)  （这个比较长，快进找重点）
- [【gin-vue-admin】V2.1.0大更新：增加字典管理，用户操作日志，从数据库一键创建CURD让自动化更加完善（1010工作室出品）](https://www.bilibili.com/video/av796356364)

## 大致了解项目结构

```
    ├─server  	     （后端文件夹）
    │  ├─api            （API）
    │  ├─config         （配置包）
    │  ├─core  	        （內核）
    │  ├─db             （数据库脚本）
    │  ├─docs  	        （swagger文档目录）
    │  ├─global         （全局对象）
    │  ├─initialiaze    （初始化）
    │  ├─middleware     （中间件）
    │  ├─model          （结构体层）
    │  ├─resource       （资源）
    │  ├─router         （路由）
    │  ├─service         (服务)
    │  └─utils	        （公共功能）
    └─web            （前端文件）
        ├─public        （发布模板）
        └─src           （源码包）
            ├─api       （向后台发送ajax的封装层）
            ├─assets	（静态文件）
            ├─components（组件）
            ├─router	（前端路由）
            ├─store     （vuex 状态管理仓）
            ├─style     （通用样式文件）
            ├─utils     （前端工具库）
            └─view      （前端页面）

```

---

## Step1：数据库初始化

1. 在本地安装mysql数据库
2. 将 server  → db → qmplus.sql 导入

## Step2：启动项目

GVA项目分为后端项目和前端项目两个。从GitHub上下载到的文件压缩包解压缩后，里边的server文件夹即后端项目文件夹，web文件夹即前端项目文件夹。

### 后端项目

- 用GoLand打开server文件夹
- 按需修改config.yaml中的七牛云、mysql等参数
- 进入GoLand的File → settings → Go → Go Modules，将 Environment 项的值设置为`GOPROXY=https://goproxy.cn/`
- 打开终端，位于目录`server/`
- 终端输入`go list`拉取依赖
- 终端输入`go get -u github.com/swaggo/swag/cmd/swag` 安装swagger
- 终端输入`swag init` 生成swagger自动化API文档
- 终端输入 `go run main.go` 运行项目（或者用IDE右上Edit Config...配置一下go build）

### 前端项目

前端是Vue项目，用vscode或者你喜欢的IDE比如WebStorm打开web文件夹，执行`npm install` （或者cnpm或者使用代理）下载相关包，然后执行 `npm run serve` 启动项目

## Step3：生成一步到位代码包

先自行设计业务基础结构体模型

点击左侧菜单中的  **系统工具 → 代码生成器** 用来生成相关代码。

- Struct名称：即后台代码中 model 文件夹下的结构体文件中，结构体的名称，首字母大写。
- tableName：数据库中生成的与结构体对应的数据表名。
- Struct简称：用于结构体作为参数时的名称，以及路由 group 名称。这里一般与**Stuct名称**对应，但是首字母小写。
- Struct中文名称：作为自动api描述，也是左侧菜单显示时的默认菜单名。
- 文件名称：使用 xxx_xxx 形式命名。生成后端代码时，model下的文件名会用这里的命名。
- 自动创建api：选中，如果不选则不会自动创建api表，需要自己去api管理里面手动增加对应路由。

如上参数填写完成后，就填写好了自动创建代码所需要的基本信息。然后点击  **新增Field**  按钮，为数据表、struct结构体创建字段。

- Field名称：struct结构体中的字段名称，首字母大写
- Field中文名：对应struct结构体tag中的comment字段值，也是数据列表展示表格的表头名称。
- FieldJSON：对应struct结构体tag中的json字段值。在使用struct对象调用某个字段时，使用“对象.json字段值”
- 数据库字段名：对应数据库中的字段名称
- 数据库字段描述：对应数据库中的列Comment值
- Field数据类型：对应struct结构体中的字段类型
- 数据库字段类型：对应生成的数据表中的字段类型
- 数据库字段长度：对应生成的数据表中的字段长度
- Field查询条件：用于实现该对象数据列表的条件查询

创建好所有的field后，点击右下角  **生成代码包**  按钮，会生成并下载一个文件压缩包。解压后会看到里面有个 autoCode 文件夹，autoCode 里的 te 文件夹为自动生成的后端代码，fe文件夹为自动生成的前端代码。

- te文件夹 → 后端代码：
  - api 下的 `.go` 文件放到后端项目中的 api → v1 下。
  - model 下的 `.go` 文件放到后端项目中的 model 下。
  - request 下的 `.go` 文件放到后端项目中的 model → request下。
  - router 下的 `.go` 文件放到后端项目中的 router 下。
  - service 下的 `.go` 文件放到后端项目中的 service 下。
- fe文件夹 → 前端代码：
  - api 下的 `.js` 文件放到前端项目中的 src>api 下。
  - table 下的 `.vue` 文件放到前端项目中的  src → view → struct 简称文件夹（自建）下。

## Step4：注册路由和数据库表

这两个操作都是在后端server项目中。

### 注册路由

进入 initialize → router.go 文件，在 `global.GVA_LOG.Info("router register success")` 这行代码前插入一行 `router.InitStruct名称Router(ApiGroup)` 。其中 `InitStruct名称Router` 需要与 router 文件夹下对应 struct 结构体（上边step中创建出来的struct结构体）的 `.go` 文件中的方法名保持一致。

### 注册数据库表

进入 initialize → db_table.go 文件，给 `db.AutoMigrate` 方法添加 `model.struct名称{}`

## Step5：配置目录菜单

进入系统  超级管理员 → 菜单管理  菜单，点击  **新增根菜单**  按钮，配置菜单信息。

- 路由name：对应进入列表显示页面时的访问路径
- 路由path：选中后边的“添加参数”后才可以输入，对应进入列表显示页面时访问路径后的参数*（没用过，目测是酱婶的）*
- 是否隐藏：是否在系统左侧目录菜单显示时，隐藏掉该目录菜单
- 父节点Id：该目录菜单的父级目录菜单。这里是自动填充的数据，不需要自己操作
- 文件路径：对应前端项目中 /view/struct简称文件夹（自建）/struct简称.vue 文件
- 展示名称：该目录菜单显示在系统左侧目录菜单中的名称
- 图标：该目录菜单显示在系统左侧目录菜单中的图标
- 排序标记：用于调整该目录菜单在系统左侧目录菜单中显示的上下位置*（我猜的）*
- keepAlive：是否使用keepAlive缓存*（太菜了，虽然知道干嘛用但是几行字解释不了，如有需要，请自行百度）*

以上配置好后，点击 **确定** 按钮，完成菜单配置。

## Step6：配置后端接口

如果是自动生成的代码，这里接口会自动配置好。

如果是自己写的业务代码，这里需要配置好后端接口。进入系统 超级管理员 → api管理 菜单，点击 **新增api** 按钮，配置接口信息。

- 路径：就是接口路径，比如前端项目中 src → api → struct简称.js 每个方法里的 url
- 请求：根据接口实际选择即可
- api分组：对应 struct 简称
- api简介：对api的简要说明

以上配置好后，点击 **确定** 按钮，完成接口配置。

## Step7：配置角色权限

进入系统 超级管理员 → 角色管理 菜单，找到需要设置权限的角色，点击对应的 **设置权限** 按钮，配置角色相关权限。

- 角色菜单：勾选该角色可以访问的目录菜单
- 角色api：勾选该角色可以访问的接口

## Step8：完善新增表单弹窗/页面

以上7个Steps后，我们可以在系统中看到我们所创建的结构体数据列表页面。目前，这个页面已经是可以实现 **删除**、**查询** 功能了，**新增**、**修改** 功能仍然需要我们自己完善一下弹窗表单。

进入系统 系统工具 → 表单生成器 菜单，根据自己的实际需求，将左侧组件拖拽至中间画布区域，并在右侧设置组件属性。

- 组件类型：默认是左侧选中的组件类型，这里还可以再进行调整
- 字段名：对应 Step3 中的 **FieldJSON** 字段
- 标题：即组件label
- 占位提示：。。。就是占位提示
- ……：*剩下的我都没试，按需自行试用*

把所有组件上边几个基本的组件属性填好以后，点击画布上方的 **复制代码**  按钮，会出现一个弹窗，让我们选择 **生成类型** 是 页面 还是 弹窗。我用的时候选的页面，具体内容应该是没差的，这里选择哪个应该不影响我们目前的需求。然后点击 **确定** 按钮，就成功复制到了我们的表单代码。

接下来，

- 随便找个记事本或者地方，把代码复制到里边。
- 在复制出来的代码中，取出 `<el-form>……</el-form>` 部分代码，覆盖掉前端项目中 src → view → struct 简称文件夹（自建） → struct简称.vue 中 `此处请使用表单生成器生成form填充 表单默认绑定 formData 如手动修改过请自行修改key` 这句话。
- 在复制出来的代码中，把 js 部分`data`方法里返回的对象复制到前端项目中，上边提及的 .vue 文件的 js 部分 `data` 方法的 `return` 对象里。（*这里表述有点乱。。但是前端一看代码就明白了。。。）*

## Finish

至此，一个单表基本业务结构体的数据列表显示，单表数据增加、删除、查找、更新功能全部搞定。

