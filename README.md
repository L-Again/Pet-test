# Pet-test

流浪动物救助与领养平台测试作品集。

本仓库用于沉淀一个真实毕业设计项目的测试过程与成果，目标是支撑软件测试岗位求职中的项目经历展示。被测项目为前后端分离的宠物救助与领养平台，包含用户注册登录、救助信息发布与审核、救助评论、管理员收编、领养宠物发布、领养申请、沟通审核、用户签收等业务流程。

## 被测项目

- 项目路径：`/Users/nolan/Developer/code/Java_code/Pet`
- 后端：Spring Boot、Java 17、MyBatis-Plus、MySQL、JWT
- 前端：Vue 3、Vite、Element Plus、Axios
- 接口风格：REST API
- 鉴权方式：`Authorization: Bearer <token>`

## 作品集结构

| 目录 | 内容 |
| --- | --- |
| `00-project-context/` | 项目背景、业务流程、接口范围 |
| `01-test-plan/` | 一日版测试执行方案 |
| `02-test-cases/` | 功能测试用例、接口测试用例 |
| `03-api-testing/` | Postman/Newman 执行说明与报告说明 |
| `04-regression-script/` | Shell + curl 回归脚本说明 |
| `05-performance-testing/` | JMeter 基础性能测试方案与报告模板 |
| `06-bug-reports/` | 缺陷记录模板 |
| `07-resume/` | 简历项目经历表达 |
| `08-interview/` | 项目介绍与面试追问准备 |
| `postman/` | Postman Collection 导出文件 |
| `jmeter/` | JMeter 测试计划文件 |
| `reports/` | 测试报告截图或导出文件 |

## 一天 MVP 目标

今天优先完成可展示闭环：

1. 整理项目背景和测试范围。
2. 写出核心功能测试用例和接口测试用例。
3. 用 Postman 执行核心接口，并导出 Collection。
4. 使用已有 `full_flow_test.sh` 跑通接口自动化回归。
5. 用 JMeter 对核心接口做基础并发测试。
6. 整理缺陷记录、测试结论、简历项目描述和面试话术。

## 注意

上传 GitHub 前不要提交真实数据库密码、JWT 密钥、Token、抓包原始文件、个人隐私数据或生产环境配置。

