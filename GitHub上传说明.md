# GitHub 上传说明

## 仓库名称

```text
Pet-test
```

## 推荐创建方式

由于当前目录 `/Users/nolan/Documents/软件测试` 位于上级 Git 仓库 `/Users/nolan/Documents` 内，如果直接在这里执行 `git add .`，容易把其他个人文件或笔记一起纳入版本控制。

推荐方式是把当前作品集目录复制到一个独立位置，例如：

```text
/Users/nolan/Developer/code/Pet-test
```

然后在独立目录中初始化 Git。

## GitHub 页面操作

1. 打开 GitHub。
2. 点击右上角 `+`。
3. 选择 `New repository`。
4. Repository name 填写：

```text
Pet-test
```

5. Description 可填写：

```text
Software testing portfolio for a pet rescue and adoption platform
```

6. Visibility 选择 `Public`。
7. 不勾选 `Add a README file`。
8. 不勾选 `.gitignore`。
9. 不选择 License。
10. 点击 `Create repository`。

## 本地上传命令

进入独立目录后执行：

```bash
git init
git branch -M main
git add .
git commit -m "docs: initialize pet testing portfolio"
git remote add origin git@github.com:<你的GitHub用户名>/Pet-test.git
git push -u origin main
```

如果你使用 HTTPS 地址，则 remote 命令改为：

```bash
git remote add origin https://github.com/<你的GitHub用户名>/Pet-test.git
```

## 上传前检查

执行：

```bash
find . -name ".DS_Store"
git status --short
```

确认不要上传：

- 数据库真实密码
- JWT 密钥
- Postman 当前环境 Token
- 抓包原始文件 `.pcapng`
- 本机绝对路径过多的临时文件
- `node_modules/`
- `target/`
- `dist/`
- `uploads/`

## 推荐上传内容

应该上传：

- README
- 项目背景
- 测试计划
- 功能测试用例
- 接口测试用例
- Postman Collection
- 脱敏环境变量示例
- Newman 报告
- JMeter 测试计划和报告
- 缺陷记录
- 简历项目经历
- 面试话术

## 不建议上传原始敏感配置

如果文档中包含本地账号、密码、Token、数据库连接信息，建议先脱敏。例如：

```text
账号：本地测试账号
密码：本地测试密码
数据库密码：已脱敏
JWT Secret：已脱敏
```

