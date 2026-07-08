# Postman 核心接口测试执行记录

## 执行准备

| 项目 | 内容 |
| --- | --- |
| 执行日期 | 2026-07-08 |
| 执行人员 | Nolan |
| 执行环境 | 本地 Spring Boot + MySQL |
| 后端地址 | `http://localhost:8080` |
| Postman 环境 | `Pet Local` |
| 测试图片 | 本地测试图片 |
| 管理员账号 | 本地测试管理员账号 |

## 环境变量

| 变量名 | 初始值 | 说明 |
| --- | --- | --- |
| `baseUrl` | `http://localhost:8080` | 后端服务地址 |
| `userToken` |  | 普通用户登录后自动保存 |
| `adminToken` |  | 管理员登录后自动保存 |
| `userId` |  | 普通用户登录后自动保存 |
| `rescueId` |  | 发布救助后自动保存 |
| `petId` |  | 管理员发布宠物后自动保存 |
| `adoptionRequestId` |  | 用户申请领养后保存 |

## 核心主流程执行结果

| 序号 | 用例编号 | 接口/操作 | 请求方式 | URL | 预期结果 | 实际结果 | 是否通过 | 证据 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | API_HEALTH_001 | 健康检查 | GET | `/api/health` | HTTP 200，`code=0` | 返回 HTTP 200，业务 `code=0` | 通过 | Postman 截图 |
| 2 | API_AUTH_001 | 普通用户注册 | POST | `/api/auth/register` | 注册成功，`code=0` | 返回 HTTP 200，业务 `code=0` | 通过 | Postman 截图 |
| 3 | API_AUTH_002 | 普通用户登录 | POST | `/api/auth/login` | 返回 `userToken` 和用户信息 | 返回 HTTP 200，业务 `code=0`，已保存 `userToken` 和 `userId` | 通过 | Postman 截图 |
| 4 | API_AUTH_003 | 管理员登录 | POST | `/api/auth/login` | 返回 `adminToken`，角色为管理员 | 返回 HTTP 200，业务 `code=0`，已保存 `adminToken` | 通过 | Postman 截图 |
| 5 | API_AUTH_004 | 获取当前用户 | GET | `/api/auth/me` | 返回当前用户信息 | 返回 HTTP 200，业务 `code=0` | 通过 | Postman 截图 |
| 6 | API_USER_001 | 修改个人资料 | PUT | `/api/users/profile` | 修改成功 | 返回 HTTP 200，业务 `code=0` | 通过 | Postman 截图 |
| 7 | API_RESCUE_001 | 发布救助信息 | POST | `/api/rescues` | 发布成功，保存 `rescueId` | 返回 HTTP 200，业务 `code=0`，已保存 `rescueId` | 通过 | Postman 截图 |
| 8 | API_ADMIN_RESCUE_001 | 审核通过救助 | POST | `/api/admin/rescues/{id}/approve` | 审核成功 | 返回 HTTP 200，业务 `code=0` | 通过 | Postman 截图 |
| 9 | API_RESCUE_002 | 查询救助列表 | GET | `/api/rescues` | 列表包含已审核救助 | 返回 HTTP 200，业务 `code=0` | 通过 | Postman 截图 |
| 10 | API_COMMENT_001 | 发布评论 | POST | `/api/rescues/{id}/comments` | 评论成功 | 返回 HTTP 200，业务 `code=0` | 通过 | Postman 截图 |
| 11 | API_ADMIN_RESCUE_002 | 管理员收编 | POST | `/api/admin/rescues/{id}/adopt` | 状态变为已收编 | 返回 HTTP 200，业务 `code=0` | 通过 | Postman 截图 |
| 12 | API_ADMIN_PET_001 | 发布领养宠物 | POST | `/api/admin/adoption/pets` | 发布成功，保存 `petId` | 返回 HTTP 200，业务 `code=0`，已保存 `petId` | 通过 | Postman 截图 |
| 13 | API_ADOPTION_001 | 查询宠物列表 | GET | `/api/adoption/pets` | 列表包含公示中宠物 | 返回 HTTP 200，业务 `code=0` | 通过 | Postman 截图 |
| 14 | API_ADOPTION_002 | 申请领养 | POST | `/api/adoption/requests` | 创建申请，保存 `adoptionRequestId` | 返回 HTTP 200，业务 `code=0` | 通过 | Postman 截图 |
| 15 | API_ADMIN_ADOPTION_001 | 管理员进入沟通 | POST | `/api/admin/adoption/requests/{id}/start` | 状态变为沟通中 | 返回 HTTP 200，业务 `code=0`，已保存 `adoptionRequestId` | 通过 | Postman 截图 |
| 16 | API_COMM_001 | 发送沟通消息 | POST | `/api/adoption/requests/{id}/messages` | 发送成功 | 返回 HTTP 200，业务 `code=0` | 通过 | Postman 截图 |
| 17 | API_ADMIN_ADOPTION_002 | 审核通过领养申请 | POST | `/api/admin/adoption/requests/{id}/approve` | 进入待签收/运输中 | 返回 HTTP 200，业务 `code=0` | 通过 | Postman 截图 |
| 18 | API_ADOPTION_003 | 用户确认签收 | POST | `/api/adoption/requests/{id}/sign` | 签收成功，流程闭环 | 返回 HTTP 200，业务 `code=0`，Postman 断言 2/2 通过 | 通过 | Postman 截图 |

## 分支与异常用例结果

| 序号 | 用例编号 | 测试点 | 预期结果 | 实际结果 | 是否通过 | 证据 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | API_ADMIN_RESCUE_003 | 管理员驳回待审核救助 | 返回成功，状态为审核驳回 | 本轮未执行 | 未执行 | 后续补充 |
| 2 | API_ADMIN_ADOPTION_003 | 管理员驳回领养申请 | 返回成功，流程闭环 | 本轮未执行 | 未执行 | 后续补充 |
| 3 | API_AUTH_NEG_001 | 登录密码错误 | 返回失败，不返回 token | 本轮未执行 | 未执行 | 后续补充 |
| 4 | API_AUTH_NEG_002 | 未携带 Token 访问 `/api/auth/me` | 返回未登录或鉴权失败 | 本轮未执行 | 未执行 | 后续补充 |
| 5 | API_AUTH_NEG_003 | 普通用户访问管理员接口 | 返回权限不足 | 本轮未执行 | 未执行 | 后续补充 |
| 6 | API_RESCUE_NEG_001 | 发布救助缺少图片 | 返回参数错误或上传失败 | 本轮未执行 | 未执行 | 后续补充 |
| 7 | API_RESCUE_NEG_002 | 发布救助地点为空 | 返回参数错误 | 本轮未执行 | 未执行 | 后续补充 |
| 8 | API_ADOPTION_NEG_001 | 重复申请同一宠物 | 返回重复申请或业务失败 | 本轮未执行 | 未执行 | 后续补充 |
| 9 | API_ADOPTION_NEG_002 | 未登录申请领养 | 返回未登录 | 本轮未执行 | 未执行 | 后续补充 |
| 10 | API_ADOPTION_NEG_003 | 非待签收状态签收 | 返回状态不允许 | 本轮未执行 | 未执行 | 后续补充 |

## 汇总结论

| 项目 | 数量 |
| --- | --- |
| 计划执行用例数 | 18 |
| 实际执行用例数 | 18 |
| 通过数 | 18 |
| 失败数 | 0 |
| 阻塞数 | 0 |

结论：

```text
本次使用 Postman 对 Pet-test 项目核心接口主流程进行测试，覆盖健康检查、普通用户注册登录、管理员登录、当前用户查询、个人资料修改、救助发布、管理员审核通过、救助列表查询、评论发布、管理员收编、管理员发布领养宠物、领养宠物列表查询、用户申请领养、管理员进入沟通、沟通消息、管理员审核通过和用户确认签收。

共执行 18 条核心接口用例，实际执行 18 条，通过 18 条，失败 0 条，阻塞 0 条。核心接口主流程返回 HTTP 200 且业务 code=0，Postman 断言通过，核心业务链路验证通过。

本轮未执行驳回分支和异常权限用例，后续可继续补充管理员驳回、登录失败、未登录访问、普通用户越权、缺少图片、重复申请等 P1 场景。
```

## 测试证据

| 证据文件 | 说明 |
| --- | --- |
| `reports/postman-main-flow-pass.png` | Postman 核心主流程最后一步签收接口返回 200 OK，断言 2/2 通过 |
| `reports/postman-environment-no-token.png` | Postman 环境变量配置截图，已清空 `userToken` 和 `adminToken` |

## 缺陷记录

发现稳定可复现问题时，同步登记到 `06-bug-reports/缺陷记录表.md`。未发现稳定可复现缺陷时，记录测试范围限制、环境限制和后续改进项。
