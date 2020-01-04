

修改日志:

| 版本号 | 内容                                                         | 日期     |
| ------ | ------------------------------------------------------------ | -------- |
| v0.1.1 | 1. 文章列表api；2. 文章详情api；3. 新增文章api               | 19-12-30 |
| v0.1.2 | 1. '全部文章'与'文章详情'的 uri 统一；2. /admin/* + 授权验证 | 19-12-30 |
| v0.1.3 | 1. 管理员登陆api                                             | 19-12-30 |
| v0.2.1 | 1. 全部分类&标签api；2. 分类&标签curd api；3. '文章列表'+'size' | 19-12-31 |
| v0.2.2 | 1. 修改'全部分类&标签'返回字段错误                           | 19-12-31 |
| v0.3.1 | 1. '单个文章'+'direction'；2. '全部文章列表','单个文章内容'='GET' | 20-01-02 |
| v0.3.2 | 1. '全部文章'-'html'；2. '新建文章'-'html'                   | 20-01-02 |
| v0.3.3 | 1. 修改文章；2. (批量)删除文章；3. (批量)恢复文章            | 20-01-03 |
|        |                                                              |          |

---

# API Introduction

API前缀: https://api.iocetc.com:8080/task

Token 认证: 

登陆后返回 token， 保存到 localStorage 里，在调需要授权的 api 时，传递 token 给服务器。方式：Http Header 新加 Authorization，内容为 "Bearer $(token)"。

Result:

| 字段名  | 说明                        | 格式   | 必须  |
| ------- | --------------------------- | ------ | ----- |
| errcode | 状态码 (0: 正常, 1: 非正常) | Number | True  |
| message | 消息                        | String | True  |
| data    | 数据                        | Object | False |

Tip:

- 如无特殊说明，除了 GET 请求，其它请求方式 params 均以 json 形式传递。
- 返回内容均为 json 形式。data 字段可能为空。请求是否正常以 errcode 为准。

---

# client

## 获取全部文章列表

api: /post

method: GET

params:

| 字段名  | 说明       | 格式   | 必须       |
| ------- | ---------- | ------ | ---------- |
| page    | 页数       | Number | False (1)  |
| size    | 每页条数   | Number | False (10) |
| keyword | 搜索关键字 | String | False      |

result:

| 字段名 | 说明     | 格式         | 必须 |
| ------ | -------- | ------------ | ---- |
| list   | 文章列表 | List<Object> | True |
| count  | 文章数量 | Number       | True |

-list:

| 字段名     | 说明         | 格式                                   | 必须 |
| ---------- | ------------ | -------------------------------------- | ---- |
| _id        | 文章 id      | Id String                              | True |
| title      | 标题         | String                                 | True |
| content    | 内容（文本） | String                                 | True |
| categoryId | 分类         | String                                 | True |
| tagId      | 标签列表     | List<String>                           | True |
| createTime | 创建时间     | Date                                   | True |
| updateTime | 上次修改时间 | Date                                   | True |
| views      | 浏览量       | Number                                 | True |
| status     | 状态         | Number (0: 已发表, 1: 草稿, 2: 已删除) | True |

tip:

## 获取单个文章内容

api: /post

method: GET

params:

| 字段名 | 说明    | 格式   | 必须 |
| ------ | ------- | ------ | ---- |
| id     | 文章 id | String | True |

result:

(见: 获取全部文章列表-list)

tip: 若 id  缺失，则返回全部文章。

## 获取当前文章的上一篇或下一篇

api: /post

method: GET

params:

| 字段名    | 说明        | 格式                                    | 必须  |
| --------- | ----------- | --------------------------------------- | ----- |
| id        | 当前文章 id | String                                  | True  |
| direction | 方向        | String ('prev': 上一篇, 'next': 下一篇) | False |

result:

(见: 获取全部文章列表-list)

## 获取全部分类与标签

api: /cat

method: GET

params: none

result:

| 字段名     | 说明     | 格式         | 必须 |
| ---------- | -------- | ------------ | ---- | 
| categories | 分类列表 | List<Object> | True |
| tags       | 标签列表 | List<Object> | True |

-categories

| 字段名 | 说明             | 格式   | 必须 |
| ------ | ---------------- | ------ | ---- |
| name   | 分类名           | String | True |
| _id    | 分类 id          | String | True |
| count  | 该分类下文章数量 | Number | True |

-tags

| 字段名 | 说明             | 格式   | 必须 |
| ------ | ---------------- | ------ | ---- |
| name   | 标签名           | String | True |
| _id    | 标签 id          | String | True |
| count  | 该标签下文章数量 | Number | True |

---

# admin

tip: 此下所有 api 需授权。

## 文章

### 修改文章

api: /admin/post

method: PUT

params:

| 字段名     | 说明         | 格式                                   | 必须  |
| ---------- | ------------ | -------------------------------------- | ----- |
| title      | 标题         | String                                 | True  |
| content    | 内容（文本） | String                                 | True  |
| categoryId | 分类id       | Id String                              | False |
| tagId      | 标签id       | Id String                              | False |
| status     | 状态         | Number (0: 已发表, 1: 草稿, 2: 已删除) | True  |

result:

| 字段名 | 说明   | 格式      | 必须 |
| ------ | ------ | --------- | ---- |
| -      | 文章id | Id String | True |

### 新建文章

api: /admin/post

method: POST

params:

| 字段名     | 说明         | 格式                                   | 必须  |
| ---------- | ------------ | -------------------------------------- | ----- |
| title      | 标题         | String                                 | True  |
| content    | 内容（文本） | String                                 | True  |
| categoryId | 分类id       | Id String                              | False |
| tagId      | 标签id       | Id String                              | False |
| status     | 状态         | Number (0: 已发表, 1: 草稿, 2: 已删除) | True  |

result:

| 字段名 | 说明   | 格式      | 必须 |
| ------ | ------ | --------- | ---- |
| -      | 文章id | Id String | True |

### 删除文章(批量)

api: /admin/post

method: DELETE

params:

| 字段名 | 说明                   | 格式                     | 必须 |
| ------ | ---------------------- | ------------------------ | ---- |
| id     | 要删除文章的id或id列表 | Id String / List<String> | True |
| truly  | 物理删除               | Boolean                  | True |

result: none

### 恢复文章(批量)

api: /admin/post

method: PATCH

params:

| 字段名 | 说明                   | 格式                     | 必须 |
| ------ | ---------------------- | ------------------------ | ---- |
| id     | 要恢复文章的id或id列表 | Id String / List<String> | True |

result: none

## 分类

### 获取分类列表

api: /admin/category

method: GET

params:

| 字段名 | 说明     | 格式   | 必须       |
| ------ | -------- | ------ | ---------- |
| page   | 页数     | Number | False (1)  |
| size   | 每页条数 | Number | False (10) |

result:

| 字段名 | 说明       | 格式         | 必须 |
| ------ | ---------- | ------------ | ---- |
| list   | 分类列表   | List<Object> | True |
| count  | 分类总数量 | Number       | True |

-list:

| 字段名 | 说明    | 格式      | 必须 |
| ------ | ------- | --------- | ---- |
| _id    | 分类 id | Id String | True |
| name   | 标题    | String    | True |

### 修改分类

api: /admin/category

method: PUT

params:

| 字段名 | 说明          | 格式      | 必须 |
| ------ | ------------- | --------- | ---- |
| id     | 待修改分类 id | Id String | True |
| value  | 分类名        | String    | True |

return: none

### 新增分类

api: /admin/category

method: POST

params:

| 字段名 | 说明   | 格式   | 必须 |
| ------ | ------ | ------ | ---- |
| value  | 分类名 | String | True |

return: none

### 删除分类

api: /admin/category

method: DELETE

params:

| 字段名 | 说明          | 格式      | 必须 |
| ------ | ------------- | --------- | ---- |
| id     | 待删除分类 id | Id String | True |

return: none

## 标签

### 获取标签列表

api: /admin/tag

method: GET

params:

| 字段名 | 说明     | 格式   | 必须       |
| ------ | -------- | ------ | ---------- |
| page   | 页数     | Number | False (1)  |
| size   | 每页条数 | Number | False (10) |

result:

| 字段名 | 说明       | 格式         | 必须 |
| ------ | ---------- | ------------ | ---- |
| list   | 标签列表   | List<Object> | True |
| count  | 标签总数量 | Number       | True |

-list:

| 字段名 | 说明    | 格式      | 必须 |
| ------ | ------- | --------- | ---- |
| _id    | 标签 id | Id String | True |
| name   | String  | True      |      |

### 修改标签

api: /admin/tag

method: PUT

params:

| 字段名 | 说明          | 格式      | 必须 |
| ------ | ------------- | --------- | ---- |
| id     | 待修改标签 id | Id String | True |
| value  | String        | True      |      |

return: none

### 新增标签

api: /admin/tag

method: POST

params:

| 字段名 | 说明   | 格式   | 必须 |
| ------ | ------ | ------ | ---- |
| tag    | 标签名 | String | True |

### 删除标签

api: /admin/tag

method: DELETE

params:

| 字段名 | 说明          | 格式      | 必须 |
| ------ | ------------- | --------- | ---- |
| id     | 待删除标签 id | Id String | True |

return: none

# auth

## 管理员登陆

api: /auth/login

method: POST

params:

| 字段名   | 说明   | 格式   | 必须 |
| -------- | ------ | ------ | ---- |
| username | 用户名 | String | True |
| password | 密码   | String | True |

return

| 字段名 | 说明  | 格式   | 必须 |
| ------ | ----- | ------ | ---- |
| -      | token | String | True |

