# JMeter 基础性能测试方案

## 测试定位

本次性能测试定位为基础并发测试，目标是验证核心接口在小规模并发下的响应时间、吞吐量和错误率，并记录本地测试环境下的性能表现。

## 测试环境

| 项目 | 内容 |
| --- | --- |
| 后端 | Spring Boot 本地服务 |
| 地址 | `http://localhost:8080` |
| 数据库 | MySQL 本地库 |
| 工具 | Apache JMeter |
| 机器 | 本地 Mac |

## 测试接口

| 优先级 | 接口 | 方法 | 说明 |
| --- | --- | --- | --- |
| P0 | `/api/health` | GET | 健康检查，验证服务基础响应 |
| P0 | `/api/auth/login` | POST | 登录接口，验证认证接口性能 |
| P0 | `/api/rescues` | GET | 救助广场列表，常用查询接口 |
| P0 | `/api/adoption/pets` | GET | 领养中心列表，常用查询接口 |
| P1 | `/api/adoption/requests` | POST | 领养申请提交，涉及写入和业务校验 |

## 压测场景

| 场景 | 线程数 | Ramp-Up | 持续时间 | 目标 |
| --- | --- | --- | --- | --- |
| S1 | 20 | 10 秒 | 1 分钟 | 验证基础并发稳定性 |
| S2 | 50 | 20 秒 | 2 分钟 | 观察响应时间和错误率变化 |
| S3 | 100 | 30 秒 | 3 分钟 | 观察本地环境下的承载边界 |

## JMeter 操作步骤

1. 新建 Test Plan：`Pet-test 基础性能测试`。
2. 添加 Thread Group。
3. 配置线程数、Ramp-Up、循环或持续时间。
4. 添加 HTTP Request Defaults：
   - Server Name：`localhost`
   - Port：`8080`
   - Protocol：`http`
5. 添加 HTTP Header Manager：
   - `Content-Type: application/json`
   - 需要鉴权的接口添加 `Authorization: Bearer <token>`
6. 添加 HTTP Request。
7. 添加 Listener：
   - View Results Tree
   - Summary Report
   - Aggregate Report
8. 先用 1 线程验证接口能通，再执行并发场景。

## 登录接口 Body 示例

```json
{
  "account": "{{testAccount}}",
  "password": "{{testPassword}}"
}
```

说明：登录接口压测使用本地测试账号，通过 JMeter 参数传入账号和密码；公开报告中仅保留脱敏后的测试数据说明。

## 记录指标

| 指标 | 含义 |
| --- | --- |
| Average | 平均响应时间 |
| 90% Line / 95% Line | P90/P95 响应时间 |
| Throughput | 吞吐量 |
| Error % | 错误率 |
| Min / Max | 最小/最大响应时间 |

## 报告结论模板

```text
本次性能测试在本地测试环境中对健康检查、登录、救助列表、领养宠物列表等核心接口进行了基础并发测试。测试重点关注平均响应时间、P95 响应时间、吞吐量和错误率。

在 xx 并发场景下，核心查询接口错误率为 xx%，平均响应时间约 xx ms，P95 响应时间约 xx ms。由于测试环境为本地开发环境，结果仅用于基础性能评估和趋势观察，不代表生产环境容量。
```
