# Newman + HTML 报告常用命令

## 1. Newman 是什么

Newman 是 Postman 官方提供的命令行运行工具。

简单理解：

```text
Postman = 图形化接口测试工具
Newman = 在终端里运行 Postman Collection 的工具
newman-reporter-html = 把 Newman 的运行结果生成 HTML 报告的插件
```

常见用途：

```text
1. 批量运行 Postman Collection
2. 在终端执行接口测试
3. 生成 HTML 测试报告
4. 配合 Jenkins / GitHub Actions 做接口自动化
5. 把测试结果保存到 GitHub 仓库，作为测试证据
```

---

## 2. 安装命令

### 2.1 安装 Newman

```bash
npm install -g newman
```

说明：

```text
-g 表示 global，也就是全局安装。
全局安装后，可以在任意目录使用 newman 命令。
```

---

### 2.2 安装 HTML 报告插件

```bash
npm install -g newman-reporter-html
```

说明：

```text
newman-reporter-html 用于把 Newman 的运行结果生成 HTML 格式测试报告。
```

---

## 3. 检查是否安装成功

### 3.1 查看 Newman 版本

```bash
newman -v
```

如果输出版本号，例如：

```text
6.x.x
```

说明 Newman 安装成功。

---

### 3.2 查看 Newman 安装路径

```bash
which newman
```

Apple Silicon Mac 常见路径：

```text
/opt/homebrew/bin/newman
```

这不是异常。

如果 Node.js / npm 是通过 Homebrew 安装的，那么 npm 全局安装的命令入口就可能在 `/opt/homebrew/bin` 目录下。

---

### 3.3 查看 npm 全局安装包

```bash
npm list -g --depth=0
```

正常应该能看到类似：

```text
newman
newman-reporter-html
```

---

## 4. 从 Postman 导出 Collection

在 Postman 中操作：

```text
1. 打开 Collection
2. 点击 Collection 右侧的 ...
3. 选择 Export
4. 选择 Collection v2.1
5. 导出为 .json 文件
```

推荐文件名：

```text
项目名.postman_collection.json
```

例如：

```text
pet-adoption.postman_collection.json
```

---

## 5. 从 Postman 导出 Environment

如果接口中使用了环境变量，例如：

```text
{{baseUrl}}
{{token}}
{{userId}}
```

就需要导出 Environment。

Postman 中操作：

```text
1. 左侧找到 Environments
2. 选择对应环境
3. 点击 ...
4. Export
5. 导出为 .json 文件
```

推荐文件名：

```text
项目名-local.postman_environment.json
```

例如：

```text
pet-adoption-local.postman_environment.json
```

---

## 6. 最基础运行命令

### 6.1 只运行 Collection

```bash
newman run collection.json
```

示例：

```bash
newman run pet-adoption.postman_collection.json
```

说明：

```text
运行整个 Postman Collection。
适合没有环境变量的简单接口集合。
```

---

## 7. 带环境变量运行

```bash
newman run collection.json -e environment.json
```

示例：

```bash
newman run pet-adoption.postman_collection.json -e pet-adoption-local.postman_environment.json
```

说明：

```text
-e 是 environment 的意思。
用于指定 Postman 环境变量文件。
```

适合接口中使用了这些变量的情况：

```text
{{baseUrl}}
{{token}}
{{username}}
{{password}}
```

---

## 8. 生成 HTML 报告

### 8.1 基础 HTML 报告

```bash
newman run collection.json -r html
```

说明：

```text
-r 是 reporter 的意思。
html 表示使用 HTML 报告插件。
```

运行后会生成默认的 HTML 报告文件。

---

## 9. 指定 HTML 报告输出路径

```bash
newman run collection.json -r html --reporter-html-export report.html
```

示例：

```bash
newman run pet-adoption.postman_collection.json -r html --reporter-html-export newman-report.html
```

说明：

```text
--reporter-html-export 用于指定 HTML 报告文件保存位置。
```

---

## 10. 带环境变量并生成 HTML 报告

这是常用命令。

```bash
newman run collection.json \
  -e environment.json \
  -r html \
  --reporter-html-export report.html
```

示例：

```bash
newman run postman/pet-adoption.postman_collection.json \
  -e postman/pet-adoption-local.postman_environment.json \
  -r html \
  --reporter-html-export docs/newman-report.html
```

说明：

```text
运行 Postman Collection
加载 Environment 环境变量
生成 HTML 测试报告
把报告保存到 docs/newman-report.html
```

---

## 11. 同时输出命令行结果和 HTML 报告

推荐使用这种格式：

```bash
newman run collection.json \
  -e environment.json \
  -r cli,html \
  --reporter-html-export report.html
```

示例：

```bash
newman run postman/pet-adoption.postman_collection.json \
  -e postman/pet-adoption-local.postman_environment.json \
  -r cli,html \
  --reporter-html-export docs/newman-report.html
```

说明：

```text
cli 表示在终端显示测试结果
html 表示生成 HTML 报告
cli,html 表示两种报告同时生成
```

这是最推荐的格式。

---

## 12. 指定请求延迟

```bash
newman run collection.json --delay-request 1000
```

说明：

```text
每个请求之间延迟 1000 毫秒，也就是 1 秒。
```

适合避免请求过快导致服务端压力过大。

示例：

```bash
newman run postman/pet-adoption.postman_collection.json \
  -e postman/pet-adoption-local.postman_environment.json \
  --delay-request 1000
```

---

## 13. 指定请求超时时间

```bash
newman run collection.json --timeout-request 10000
```

说明：

```text
单个请求最多等待 10000 毫秒，也就是 10 秒。
```

示例：

```bash
newman run postman/pet-adoption.postman_collection.json \
  -e postman/pet-adoption-local.postman_environment.json \
  --timeout-request 10000
```

---

## 14. 忽略 SSL 证书错误

```bash
newman run collection.json --insecure
```

说明：

```text
如果测试 HTTPS 接口时遇到证书问题，可以临时使用。
```

注意：

```text
本地测试可以用。
正式环境不要随便忽略证书错误。
```

---

## 15. 运行指定文件夹

如果 Collection 里有多个文件夹，例如：

```text
用户模块
宠物模块
领养模块
后台审核模块
```

可以只运行某一个文件夹。

```bash
newman run collection.json --folder "用户模块"
```

示例：

```bash
newman run postman/pet-adoption.postman_collection.json \
  -e postman/pet-adoption-local.postman_environment.json \
  --folder "用户模块"
```

说明：

```text
只运行 Collection 中名为“用户模块”的接口。
```

---

## 16. 运行多次 Collection

```bash
newman run collection.json -n 3
```

说明：

```text
-n 是 iteration count。
表示整个 Collection 运行 3 次。
```

示例：

```bash
newman run postman/pet-adoption.postman_collection.json \
  -e postman/pet-adoption-local.postman_environment.json \
  -n 3
```

---

## 17. 使用数据文件运行

适合参数化测试。

例如有一个数据文件：

```text
users.json
```

内容示例：

```json
[
  {
    "username": "test01",
    "password": "123456"
  },
  {
    "username": "test02",
    "password": "123456"
  }
]
```

运行命令：

```bash
newman run collection.json -d users.json
```

完整示例：

```bash
newman run postman/login.postman_collection.json \
  -e postman/local.postman_environment.json \
  -d data/users.json \
  -r cli,html \
  --reporter-html-export docs/login-test-report.html
```

说明：

```text
-d 是 data 的意思。
用于导入测试数据文件。
Newman 会根据数据文件中的多组数据重复执行接口。
```

---

## 18. 常用目录结构推荐

如果要放到 GitHub 仓库中，推荐这样整理：

```text
project-name/
├── backend/
├── frontend/
├── docs/
│   ├── 接口文档.md
│   ├── 测试计划.md
│   ├── 测试用例.md
│   ├── 缺陷记录.md
│   └── newman-report.html
├── postman/
│   ├── project.postman_collection.json
│   └── project-local.postman_environment.json
├── data/
│   └── users.json
└── README.md
```

说明：

```text
postman/ 存放 Collection 和 Environment
docs/ 存放测试文档和测试报告
data/ 存放参数化测试数据
README.md 说明如何运行测试
```

---

## 19. README 中可以这样写

```md
## 接口测试

本项目使用 Postman + Newman 进行接口测试。

测试范围包括：

- 用户登录接口
- 用户注册接口
- 用户信息查询接口
- 宠物列表查询接口
- 宠物详情接口
- 领养申请接口
- 后台审核接口

测试覆盖点包括：

- HTTP 状态码校验
- 响应字段校验
- 业务状态码校验
- Token 鉴权校验
- 必填参数缺失校验
- 异常参数校验

运行方式：

\```bash
newman run postman/project.postman_collection.json \
  -e postman/project-local.postman_environment.json \
  -r cli,html \
  --reporter-html-export docs/newman-report.html
\```

测试报告：

\```text
docs/newman-report.html
\```
```

---

## 20. Postman Tests 常用断言

Newman 的测试结果来自 Postman 里的 Tests 脚本。

---

### 20.1 校验状态码是 200

```javascript
pm.test("状态码应为 200", function () {
    pm.response.to.have.status(200);
});
```

---

### 20.2 校验响应时间小于 1000ms

```javascript
pm.test("响应时间应小于 1000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(1000);
});
```

---

### 20.3 校验返回结果是 JSON

```javascript
pm.test("响应格式应为 JSON", function () {
    pm.response.to.be.json;
});
```

---

### 20.4 校验字段存在

```javascript
pm.test("响应中应包含 data 字段", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData).to.have.property("data");
});
```

---

### 20.5 校验业务状态码

假设响应是：

```json
{
  "code": 200,
  "message": "success",
  "data": {}
}
```

断言：

```javascript
pm.test("业务状态码应为 200", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData.code).to.eql(200);
});
```

---

### 20.6 校验 Token 存在

```javascript
pm.test("响应中应返回 token", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData.data).to.have.property("token");
});
```

---

### 20.7 保存 Token 到环境变量

```javascript
const jsonData = pm.response.json();

if (jsonData.data && jsonData.data.token) {
    pm.environment.set("token", jsonData.data.token);
}
```

然后后续接口 Headers 中可以使用：

```text
Authorization: Bearer {{token}}
```

---

## 21. 常见完整命令模板

### 21.1 模板一：最基础运行

```bash
newman run postman/project.postman_collection.json
```

---

### 21.2 模板二：带环境变量运行

```bash
newman run postman/project.postman_collection.json \
  -e postman/project-local.postman_environment.json
```

---

### 21.3 模板三：生成 HTML 报告

```bash
newman run postman/project.postman_collection.json \
  -e postman/project-local.postman_environment.json \
  -r cli,html \
  --reporter-html-export docs/newman-report.html
```

---

### 21.4 模板四：带延迟、超时、HTML 报告

```bash
newman run postman/project.postman_collection.json \
  -e postman/project-local.postman_environment.json \
  -r cli,html \
  --delay-request 1000 \
  --timeout-request 10000 \
  --reporter-html-export docs/newman-report.html
```

---

### 21.5 模板五：只运行某个模块

```bash
newman run postman/project.postman_collection.json \
  -e postman/project-local.postman_environment.json \
  --folder "用户模块" \
  -r cli,html \
  --reporter-html-export docs/user-module-report.html
```

---

### 21.6 模板六：使用数据文件

```bash
newman run postman/project.postman_collection.json \
  -e postman/project-local.postman_environment.json \
  -d data/users.json \
  -r cli,html \
  --reporter-html-export docs/data-driven-report.html
```

---

## 22. 卸载命令

### 22.1 卸载 Newman 和 HTML 报告插件

```bash
npm uninstall -g newman newman-reporter-html
```

---

### 22.2 检查是否卸载干净

```bash
newman -v
```

如果显示：

```text
command not found: newman
```

说明 Newman 命令已经不存在。

再检查：

```bash
which newman
```

没有输出说明命令入口已经删除。

查看全局 npm 包：

```bash
npm list -g --depth=0
```

确认里面没有：

```text
newman
newman-reporter-html
```

---

### 22.3 可选：清理 npm 缓存

```bash
npm cache clean --force
```

说明：

```text
不是必须。
清理后以后安装 npm 包会重新下载。
```

---

## 23. 常见问题

### 23.1 npm warn deprecated 是不是报错？

不是。

```text
npm warn deprecated = 依赖包过旧警告
```

只要最后出现：

```text
added xxx packages
```

通常说明安装成功。

---

### 23.2 8 packages are looking for funding 是什么意思？

意思是有些开源包接受赞助。

不是报错，不用处理。

---

### 23.3 为什么 npm 安装的 Newman 路径在 /opt/homebrew/bin？

因为你的 Node.js / npm 很可能是通过 Homebrew 安装的。

逻辑是：

```text
Homebrew 安装 Node.js / npm
npm 全局安装 Newman
Newman 的命令入口放到 npm 的全局 bin 目录
你的 npm 全局 bin 目录刚好是 /opt/homebrew/bin
```

所以这不是异常。

---

### 23.4 HTML 报告能不能放 GitHub？

可以。

建议放在：

```text
docs/newman-report.html
```

同时把 Collection 和 Environment 也上传：

```text
postman/project.postman_collection.json
postman/project-local.postman_environment.json
```

否则只有报告，证明力不够。

---

### 23.5 HTML 报告能证明什么？

它可以证明：

```text
1. 你写过接口测试集合
2. 你执行过接口测试
3. 你有接口测试结果
4. 你会使用 Postman + Newman 做批量接口测试
```

但它不能单独证明：

```text
1. 你真的理解业务
2. 你真的会设计测试用例
3. 你真的会定位 Bug
4. 你真的有完整测试思维
```

所以最好同时准备：

```text
1. 接口文档
2. 测试计划
3. 测试用例
4. 缺陷记录
5. Postman Collection
6. Newman HTML 报告
```

---

## 24. 学习优先级

不要把安装工具当成学习成果。

正确顺序是：

```text
1. Postman 会发 GET / POST / PUT / DELETE 请求
2. 会传 Params / Headers / Body
3. 会看 Response 和状态码
4. 会保存 Collection
5. 会使用 Environment 环境变量
6. 会写 Tests 断言
7. 会导出 Collection
8. 会用 Newman 批量运行
9. 会生成 HTML 报告
10. 会把测试文档和报告整理进 GitHub
```

真正有求职价值的是这条闭环：

```text
接口文档
→ 测试用例
→ Postman Collection
→ Tests 断言
→ Newman 批量运行
→ HTML 测试报告
→ 缺陷记录
→ GitHub README 说明
```

---

## 25. 最推荐的 Newman 命令

如果项目已经有 Collection 和 Environment，最推荐使用：

```bash
newman run postman/project.postman_collection.json \
  -e postman/project-local.postman_environment.json \
  -r cli,html \
  --reporter-html-export docs/newman-report.html
```

这条命令的含义：

```text
newman run
运行 Postman Collection

postman/project.postman_collection.json
指定要运行的接口集合文件

-e postman/project-local.postman_environment.json
指定环境变量文件

-r cli,html
同时在终端输出结果，并生成 HTML 报告

--reporter-html-export docs/newman-report.html
指定 HTML 报告保存路径
```

---

## 26. 我的当前安装记录

安装 Newman：

```bash
npm install -g newman
```

安装 HTML 报告插件：

```bash
npm install -g newman-reporter-html
```

检查命令：

```bash
newman -v
which newman
npm list -g --depth=0
```

卸载命令：

```bash
npm uninstall -g newman newman-reporter-html
```

---

## 27. 结论

Newman + HTML 报告插件不是接口测试入门的第一优先级，但它能把 Postman 接口测试从“手动点 Send”推进到“批量运行 + 自动生成报告”。

对求职项目来说，价值在于：

```text
1. 让项目更像完整交付物
2. 证明做过接口测试
3. 方便面试时展示测试过程
4. 方便 GitHub 仓库展示测试成果
5. 为后续 Jenkins / GitHub Actions 自动化测试打基础
```

但是，不要误判重点。

真正重要的不是装了 Newman，而是：

```text
你是否会设计接口测试用例
你是否会写 Postman Tests 断言
你是否能解释每个接口为什么这样测
你是否能记录缺陷、定位问题、说明修复过程
```

工具只是证据载体，不是能力本身。