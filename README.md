# Lost & Found（uni-app‑x + uniCloud）

一个基于 uni-app‑x 的校园失物招领模块，支持「独立小程序/APP」与「主项目子包/组件集成」两种模式。默认启用本地 JSON 数据以便快速预览；可切换到云端（uniCloud‑alipay）完成完整发布、审核、认领与消息闭环。

## 功能概览
- 发布与寻物：拍照上传、填写特征、类型选择、定位地址、可选托管招领点
- 列表与筛选：关键词、类型、状态、时间范围筛选；分页与懒加载
- 详情页：物品信息、照片、地址与时间、招领点标签、认领入口
- 认领流程：预约时段、线下核验、状态流转（云函数流程可扩展）
- 消息中心：通知与会话视图，后续支持关键词屏蔽与会话管理
- 招领点：点位详情（地图/电话）、点位物品列表、点位启用/停用、导出
- 登录：集成 `uni-id-pages-x` 最小登录（用户名/短信），可扩展 APP 一键登录

## 技术栈
- 前端：uni-app‑x（Vue3 + uvue 模板 + UTS）
- 云端：uniCloud‑alipay（UTS 云函数 + 云数据库）
- 类型与工具：严格 UTS 类型定义与工具模块（地址、定位、文本安全）

## 目录结构
- 根入口与基本配置：`App.uvue`、`main.uts`、`index.html`、`manifest.json`、`pages.json`、`package.json`、`uni.scss`
- 页面与组件：
  - 主页与导航：`pages/lost/home/index.uvue`、`pages/lost/list/index.uvue`、`pages/lost/all/index.uvue`、`pages/lost/search/index.uvue`
  - 详情与发布：`pages/lost/detail/index.uvue`、`pages/lost/publish/index.uvue`
  - 消息与我的：`pages/lost/notice/index.uvue`、`pages/lost/user/index.uvue`
  - 招领点：`pages/lost/point/detail/index.uvue`、`pages/lost/point/manage.uvue`
  - 组件：`components/lost/filter-bar.uvue`、`components/lost/photo-grid.uvue`、`components/lost/action-bar.uvue`
- UTS 工具与类型：
  - 客户端接口封装：`uts/lost/api.uts`（含本地/云端切换）
  - 本地数据实现：`uts/lost/local.uts`
  - 地址与定位：`uts/lost/address.uts`、`uts/lost/location.uts`
  - 文本安全：`uts/lost/textGuard.uts`
  - 类型定义：`types/lost/types.uts`
- 静态本地数据：`static/data/lost_item.json`、`static/data/lost_point.json`、`static/data/lost_announce.json`
- uniCloud‑alipay：
  - 公共权限工具：`uniCloud-alipay/cloudfunctions/common/perm.uts`
  - 失物模块云函数（占位/可扩展）：`uniCloud-alipay/cloudfunctions/lost/*`
  - 数据库 schema：`uniCloud-alipay/database/*.schema.json`
- 登录最小模块：`uni_modules/uni-id-pages-x/pages/login/login.uvue`、`uni_modules/uni-id-pages-x/config.uts`、`uni_modules/uni-id-pages-x/store.uts`

## 页面清单（pages.json）
- 失物招领：`pages/lost/list/index`（导航文案：失物招领）
- 详情：`pages/lost/detail/index`
- 发布：`pages/lost/publish/index`
- 主页：`pages/lost/home/index`
- 搜索：`pages/lost/search/index`
- 全部信息：`pages/lost/all/index`
- 消息中心：`pages/lost/notice/index`
- 我的：`pages/lost/user/index`
- 审核中心：`pages/lost/audit/index`
- 招领点管理：`pages/lost/point/manage`
- 暂存点详情：`pages/lost/point/detail/index`
- 登录页（uni-id-pages-x）：`uni_modules/uni-id-pages-x/pages/login/login`

tabBar：消息（`pages/lost/notice/index`）、我的（`pages/lost/user/index`）

## 快速开始
1. 环境准备
   - HBuilderX（含 uni-app‑x 支持，建议 3.98+），导入本项目后可运行至 H5/小程序/APP
   - 默认走本地数据，无需云配置即可体验核心页面与流程
2. 本地数据切换
   - 本地 JSON：`static/data/*.json`
   - 客户端开关：`uts/lost/api.uts` 中 `useLocal = true`
   - 切换到云端：将其改为 `false`，并完成 uniCloud 配置与云函数部署
3. 页面导航
   - 底部 tabBar：消息 / 我的
   - 发布入口：主页右上角或 `pages/lost/publish/index`

## 切换到云端（uniCloud‑alipay）
1. 开通云空间并在 HBuilderX 关联 uniCloud（支付宝云）
2. 在 `manifest.json` 配置对应平台权限与 AppID
3. 部署云函数（`uniCloud-alipay/cloudfunctions/lost/*`）
   - 示例：`lost_item_list`、`lost_item_detail`、`lost_item_create`、`lost_point_list` 等占位函数
4. 配置数据库集合（`uniCloud-alipay/database/`）
   - 已包含：`lost_item.schema.json`、`lost_point.schema.json`、`lost_message.schema.json`、`lost_claim.schema.json`、`lost_conversation.schema.json`、`user_permission.schema.json`
   - 索引建议：`status`、`point_id`、`type1`、`created_at`、`user_id`
5. 客户端对接
   - 统一返回结构：`{ code, msg, data }`
   - 错误码前缀：`lost_`（如 `lost_401` 未登录、`lost_404` 未找到、`lost_500` 服务错误）

## 云函数与接口对照
- 列表页：`pages/lost/list/index.uvue` ↔ `uts/lost/api.uts:listItems` ↔ `cloudfunctions/lost/lost_item_list`
- 详情页：`pages/lost/detail/index.uvue` ↔ `uts/lost/api.uts:getItem` ↔ `cloudfunctions/lost/lost_item_detail`
- 发布页：`pages/lost/publish/index.uvue` ↔ `uts/lost/api.uts:createItem` ↔ `cloudfunctions/lost/lost_item_create`
- 招领点：`pages/lost/point/detail/index.uvue` ↔ `uts/lost/api.uts:listPoints`

## 数据模型（types/lost/types.uts）
- `LostItem`：基本信息、照片、类型、状态（0 待认领、1 已认领、2 已失效）、位置与时间
- `LostPoint`：招领点（坐标、开放时间、联系人）
- `LostClaim`：认领申请（时段、核验、状态）
- `LostMessage` / `Conversation`：消息与会话（后续接入）

## 客户端隐私与安全
- 地址模糊化：`uts/lost/address.uts` 的 `maskAddress`
- 地图导航：统一使用 `uni.openLocation`（`uts/lost/location.uts`）
- 聊天屏蔽词：`uts/lost/textGuard.uts`（屏蔽支付相关关键词）

## 权限与登录
- 登录页：`uni_modules/uni-id-pages-x/pages/login/login.uvue`
- 登录状态与用户信息：`uni_modules/uni-id-pages-x/store.uts`
- 云端权限工具：`uniCloud-alipay/cloudfunctions/common/perm.uts`
  - `assertLogin(uid)` 强制登录校验
  - `getUserPerms(uid)` 拉取权限码（示例集合：`user_permission`）

## 模块集成规范
- 在 `package.json` 中声明模块信息与入口：
  - `uni-app-x.module.entry = pages/lost/list/index`
  - 支持 `deployMode.standalone` 与 `deployMode.mainProject`
- 资源隔离与页面前缀：`pages/lost/**`；组件位于 `components/lost/**`
- 与主项目通信：组件模式（`props`/`emit`）、子包模式（`eventChannel`）

## 开发约束（uni-app‑x）
- 严格 UTS 静态类型，不使用 `any`
- 模板使用 uvue + Vue3 组合式 API；样式仅用 flex 相关属性且 `scoped`
- 所有 API 调用需异常处理（`try/catch` 或 `fail` 回调），禁止在客户端存储敏感信息
- 云函数统一返回 `{ code, msg, data }`，错误码前缀 `lost_`

## 运行与调试建议
- 使用 HBuilderX 运行到 H5/小程序/APP 进行跨端验证
- 切换本地/云端时同步检查：筛选、分页、导航、认领流程
- 关注慢查询：为 `user_id`、`created_at` 等建立索引，避免全表扫描

## 路线图
- 完成云函数实现与联调（列表、详情、发布、审核、认领、消息）
- 打通 uni-id 真实登录与权限校验
- 招领点地理位置联动（距离排序、推荐托管）
- 导出 CSV、批量标记等点位管理增强

## 许可协议
- 本项目采用 MIT 许可

---
如需我继续完善云函数与 `uni-id-pages-x` 全量模块、或将最新 README 推送到 GitHub 仓库，请直接告知。
