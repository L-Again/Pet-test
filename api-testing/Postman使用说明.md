# Postman 使用说明

## 文件说明

| 文件 | 说明 |
| --- | --- |
| `Pet-test.postman_collection.json` | 已导出的 Postman Collection |
| `接口测试用例.md` | 核心接口测试用例 |
| `接口测试执行报告.md` | Postman 实际执行结果 |

本仓库不保留 Postman 本地环境文件，避免误提交 Token、账号密码或本机环境信息。

## 环境变量

测试环境名称：`Pet Local`

| 变量名 | 示例值 | 说明 |
| --- | --- | --- |
| `baseUrl` | `http://localhost:8080` | 后端服务地址 |
| `userToken` | 执行登录后自动保存 | 普通用户 Token |
| `adminToken` | 执行登录后自动保存 | 管理员 Token |
| `userId` | 执行登录后自动保存 | 普通用户 ID |
| `rescueId` | 发布救助后自动保存 | 救助记录 ID |
| `petId` | 管理员发布宠物后自动保存 | 宠物 ID |
| `adoptionRequestId` | 用户申请领养或管理员查询后保存 | 领养申请 ID |

## 登录接口 Token 提取

普通用户登录接口 `Tests` 示例：

```javascript
pm.test("状态码为 200", function () {
  pm.response.to.have.status(200);
});

const jsonData = pm.response.json();

pm.test("业务 code 为 0", function () {
  pm.expect(jsonData.code).to.eql(0);
});

pm.test("返回 token", function () {
  pm.expect(jsonData.data).to.have.property("token");
});

pm.environment.set("userToken", jsonData.data.token);
pm.environment.set("userId", jsonData.data.user.id);
```

管理员登录接口保存管理员 Token：

```javascript
pm.environment.set("adminToken", jsonData.data.token);
```

## 通用断言模板

```javascript
pm.test("状态码为 200", function () {
  pm.response.to.have.status(200);
});

pm.test("响应格式为 JSON", function () {
  pm.response.to.be.json;
});

const jsonData = pm.response.json();

pm.test("业务 code 为 0", function () {
  pm.expect(jsonData.code).to.eql(0);
});
```

## 执行顺序

1. 健康检查
2. 普通用户注册
3. 普通用户登录，保存 `userToken` 和 `userId`
4. 管理员登录，保存 `adminToken`
5. 获取当前用户
6. 修改个人资料
7. 发布救助信息，保存 `rescueId`
8. 管理员审核通过救助
9. 查询救助广场列表
10. 用户发表评论
11. 管理员收编救助信息
12. 管理员发布领养宠物，保存 `petId`
13. 查询领养宠物列表
14. 用户申请领养
15. 管理员查询领养申请，保存 `adoptionRequestId`
16. 管理员进入沟通
17. 管理员发送沟通消息
18. 用户发送沟通消息
19. 管理员审核通过领养申请
20. 用户确认签收

## 截图证据

![Postman 核心主流程通过](screenshots/postman-main-flow-pass.png)

![Postman 环境变量脱敏](screenshots/postman-environment-no-token.png)
