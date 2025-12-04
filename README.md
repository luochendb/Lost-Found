# 失物招领模块（uni-app-x + uniCloud）

## 独立上线
- 在 `manifest.json` 中配置对应平台的 AppID 与权限；
- 云端部署 `uniCloud/cloudfunctions/lost/**`，并创建数据库集合（前缀 `lost_`）。

## 主项目集成
- 作为子包引入：页面路径前缀 `pages/lost/**`；或组件嵌入方式按 props/emit 交互。
- 权限依赖 `uni-id` 与 `user_permission` 的权限码：`lost:pointAdmin`、`lost:superAdmin`。

## 开发规范
- 严格 UTS 类型与组合式 API；模板样式使用 `scoped` 与 flex 布局。
- 云函数统一返回结构 `{ code, msg, data }`，错误码前缀 `lost_`。

## 地图与隐私
- 发布页调用定位填充地址；详情/认领页仅提供导航按钮。
- 模糊地址展示，精确位置仅在认领后可见；手机号仅在认领后对拾获者/管理员可见。