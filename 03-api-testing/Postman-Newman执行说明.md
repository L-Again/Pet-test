# Postman / Newman 执行说明

## Postman 环境变量

测试环境：`Pet Local`

| 变量名 | 示例值 | 说明 |
| --- | --- | --- |
| `baseUrl` | `http://localhost:8080` | 后端服务地址 |
| `userToken` | 执行登录后自动保存 | 普通用户 Token |
| `adminToken` | 执行登录后自动保存 | 管理员 Token |
| `rescueId` | 发布救助后自动保存 | 救助记录 ID |
| `petId` | 管理员发布宠物后自动保存 | 宠物 ID |
| `adoptionRequestId` | 用户申请领养后保存 | 领养申请 ID |

## 登录接口 Token 提取示例

普通用户登录接口 `Tests`：

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
```

管理员登录接口把最后一行改为：

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
2. 注册普通用户
3. 普通用户登录，保存 `userToken`
4. 管理员登录，保存 `adminToken`
5. 获取当前用户
6. 用户发布救助，保存 `rescueId`
7. 管理员审核通过救助
8. 查询救助广场列表
9. 用户发表评论
10. 管理员收编救助
11. 管理员发布领养宠物，保存 `petId`
12. 用户申请领养，保存 `adoptionRequestId`
13. 管理员进入沟通
14. 双方发送消息
15. 管理员审核通过
16. 用户确认签收
17. 执行驳回分支用例
18. 执行权限与异常用例

## Newman 命令

导出 Collection 后执行：

```bash
newman run postman/Pet-test.postman_collection.json \
  -e postman/Pet-test-local.postman_environment.json \
  -r cli,html \
  --reporter-html-export reports/newman/Pet-test-newman-report.html
```

环境文件导出前应进行脱敏处理，避免保留 Token、本机绝对路径或真实账号信息。本仓库仅保留 Collection、执行说明、脱敏环境示例和测试报告。
