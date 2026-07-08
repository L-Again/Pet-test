# Wireshark 基础使用笔记

## 一、Wireshark 是什么

Wireshark 是一款网络抓包与协议分析工具。

它的核心作用不是“测试接口”，而是捕获网络数据包，辅助定位网络层和请求链路问题。

在后端开发、软件测试、运维、网络排查中，Wireshark 常用于分析：

- 请求是否真的发出
- 服务器是否有响应
- TCP 连接是否建立
- 是否存在重传、超时、连接失败
- DNS 是否正常解析
- HTTP 请求和响应内容
- HTTPS/TLS 握手过程

我的使用定位：

```text
Postman：接口测试主力工具
浏览器 DevTools：前后端联调排查工具
Wireshark：抓包定位辅助工具
后端日志：代码异常定位工具
MySQL：数据校验工具
```

不要把 Wireshark 当作接口测试工具。接口测试应该使用 Postman / Apifox，Wireshark 用来辅助定位问题。

---

## 二、安装方式

### 1. Homebrew 安装

终端执行：

```bash
brew install --cask wireshark
```

安装成功后，终端会出现类似：

```text
wireshark-app was successfully installed!
```

### 2. 打开方式

可以通过以下方式打开：

```text
访达 Finder → 应用程序 Applications → Wireshark
```

或者：

```text
Command + 空格 → 输入 Wireshark → 回车
```

---

## 三、macOS 后台权限组件

安装完成后，macOS 可能会提示：

```text
Wireshark Foundation 的软件在后台运行
```

这是正常的。

Wireshark 在 macOS 上抓包需要访问底层网络接口，因此会安装后台权限组件，通常与 ChmodBPF 有关。

结论：

```text
Wireshark 主程序不需要常开
Wireshark Foundation 后台权限组件可以保留
```

不建议关闭，否则后续可能出现无法抓包、没有权限访问网卡等问题。

---

## 四、主界面基础认识

打开 Wireshark 后，会看到多个网络接口。

常见接口：

| 接口 | 作用 |
|---|---|
| Wi-Fi: en0 | 抓取正常联网流量 |
| Loopback: lo0 | 抓取本机 localhost / 127.0.0.1 流量 |
| awdl0 | Apple 设备相关接口，暂时不用管 |
| utun0 / utun1 / utun2 | VPN、代理、隧道相关虚拟网卡 |
| llw0 | 系统网络相关接口，暂时不用管 |

当前阶段只需要重点掌握两个：

```text
Wi-Fi: en0
Loopback: lo0
```

---

## 五、什么时候选哪个网卡

### 1. 抓外网请求

如果访问的是：

```text
https://www.baidu.com
https://github.com
http://服务器IP:8080
```

优先选择：

```text
Wi-Fi: en0
```

### 2. 抓本地项目请求

如果项目是本地运行：

```text
前端：http://localhost:5173
后端：http://localhost:8080
```

应该选择：

```text
Loopback: lo0
```

因为 `localhost` 和 `127.0.0.1` 走的是本机回环接口，不是 Wi-Fi。

---

## 六、基本操作按钮

| 图标 | 作用 |
|---|---|
| 蓝色鲨鱼鳍 | 开始抓包 |
| 红色方块 | 停止抓包 |
| 绿色鲨鱼鳍 | 重新开始抓包 |
| 文件夹 | 打开已有抓包文件 |
| 保存图标 | 保存当前抓包文件 |

使用习惯：

```text
需要排查问题 → 打开 Wireshark → 选择网卡 → 开始抓包 → 复现问题 → 停止抓包 → 分析 → 是否保存
```

不要一直开着 Wireshark 抓包。

原因：

```text
1. 会持续占用内存和 CPU
2. 数据包越来越多，分析会变乱
3. 抓包文件可能包含敏感信息
```

---

## 七、常见弹窗说明

### 1. 是否保存当前抓包

弹窗内容：

```text
Do you want to save the captured packets before starting a new capture?
Your captured packets will be lost if you don't save them.
```

意思是：

```text
你已经抓到了一批数据包
现在要开始新的抓包
如果不保存，之前的数据包会被清空
```

三个按钮含义：

| 按钮 | 含义 |
|---|---|
| Continue without Saving | 不保存，直接开始新抓包 |
| Cancel | 取消操作 |
| Save | 保存当前抓包文件 |

如果只是练习，可以选：

```text
Continue without Saving
```

如果是排查真实问题，可以选：

```text
Save
```

保存格式一般是：

```text
.pcapng
```

---

### 2. BIOCPROMISC 报错

可能出现：

```text
BIOCPROMISC: Operation not supported on socket.
```

意思是：

```text
Wireshark 尝试开启混杂模式，但当前网卡或系统不支持
```

这不是严重错误，不代表安装失败。

解决方式：

```text
Capture → Options → 找到 Wi-Fi: en0 → 取消勾选 Promiscuous Mode
```

当前学习阶段不需要混杂模式。

---

## 八、什么是混杂模式 Promiscuous Mode

普通模式下，网卡主要接收：

```text
发给本机的数据包
本机发出去的数据包
广播/组播数据包
```

混杂模式尝试让网卡接收更多经过网卡的数据包。

但在现代 macOS 和 Wi-Fi 环境下，经常不支持或受限制。

对当前学习目标来说：

```text
不需要开启混杂模式
```

因为我要抓的是自己的项目、自己的请求、自己的网络流量。

---

## 九、显示过滤器 Display Filter

Wireshark 顶部的输入框是显示过滤器。

作用：

```text
抓包已经开始后，只显示符合条件的数据包
```

常用过滤器如下。

### 1. 只看 TCP

```text
tcp
```

### 2. 只看 HTTP

```text
http
```

### 3. 只看 HTTP 请求

```text
http.request
```

### 4. 只看 HTTP 响应

```text
http.response
```

### 5. 只看 DNS

```text
dns
```

### 6. 只看 TLS / HTTPS 握手

```text
tls
```

### 7. 只看 8080 端口

```text
tcp.port == 8080
```

适合抓 Spring Boot 后端接口。

### 8. 同时看 8080 和 5173

```text
tcp.port == 8080 || tcp.port == 5173
```

适合本地前后端联调：

```text
Vue: 5173
Spring Boot: 8080
```

### 9. 只看某个 IP

```text
ip.addr == 192.168.1.10
```

如果是服务器 IP，可以写：

```text
ip.addr == 服务器IP
```

### 10. 只看某个端口的 HTTP

```text
tcp.port == 8080 && http
```

---

## 十、基础抓包流程

### 场景一：抓外网 DNS

目的：验证 Wireshark 是否能正常抓包。

步骤：

```text
1. 打开 Wireshark
2. 双击 Wi-Fi: en0
3. 顶部过滤器输入 dns
4. 打开浏览器访问一个新网站
5. 观察是否出现 DNS 查询数据包
```

如果能看到 DNS 数据包，说明基础抓包正常。

---

### 场景二：抓本地 Spring Boot 接口

适合毕设项目测试。

假设项目：

```text
前端：http://localhost:5173
后端：http://localhost:8080
```

步骤：

```text
1. 启动 Spring Boot 后端
2. 打开 Wireshark
3. 双击 Loopback: lo0
4. 顶部过滤器输入 tcp.port == 8080
5. 用浏览器或 Postman 请求接口
6. 观察是否出现 8080 端口相关数据包
7. 抓到后点击红色方块停止抓包
```

---

### 场景三：抓登录接口

假设登录接口：

```text
POST /api/user/login
```

操作流程：

```text
1. 启动后端
2. 打开 Wireshark
3. 选择 Loopback: lo0
4. 输入过滤器 tcp.port == 8080
5. 用 Postman 请求登录接口
6. 查看是否出现请求数据包
7. 停止抓包
8. 分析请求和响应
```

---

## 十一、如何判断请求是否发出

### 1. 看是否有数据包出现

如果过滤器是：

```text
tcp.port == 8080
```

请求接口后有数据包，说明：

```text
客户端确实向 8080 端口发起了网络请求
```

如果没有数据包，可能是：

```text
1. 前端根本没有发请求
2. 请求地址写错
3. 端口写错
4. Axios baseURL 配错
5. 浏览器拦截
6. 选错了网卡
```

本地项目要选：

```text
Loopback: lo0
```

外网或服务器接口要选：

```text
Wi-Fi: en0
```

---

### 2. 看 TCP 是否连接成功

正常 TCP 建立连接会出现：

```text
SYN
SYN, ACK
ACK
```

如果不断出现：

```text
SYN
SYN
SYN
```

但没有响应，可能是：

```text
1. 服务端没有启动
2. 端口不通
3. 防火墙拦截
4. 云服务器安全组没开放
5. IP 或端口写错
```

---

### 3. 看 HTTP 请求

过滤器：

```text
http.request
```

如果能看到类似：

```text
POST /api/user/login HTTP/1.1
GET /api/xxx HTTP/1.1
```

说明 HTTP 请求已经发出。

---

### 4. 看 HTTP 响应

过滤器：

```text
http.response
```

常见状态码：

| 状态码 | 含义 |
|---|---|
| 200 | 成功 |
| 400 | 参数错误 |
| 401 | 未登录 / Token 错误 |
| 403 | 权限不足 |
| 404 | 接口地址不存在 |
| 405 | 请求方法错误 |
| 500 | 后端代码异常 |

注意：

```text
状态码判断优先看 Postman / 浏览器 Network
Wireshark 作为辅助证据
```

---

## 十二、Follow TCP Stream

`Follow TCP Stream` 用来查看一次 TCP 会话中的请求和响应。

操作：

```text
1. 点击某一条 HTTP 请求数据包
2. 右键
3. Follow
4. TCP Stream
```

如果是 HTTP 明文，可能看到：

```http
POST /api/user/login HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{"username":"admin","password":"123456"}
```

以及响应：

```http
HTTP/1.1 200
Content-Type: application/json

{"code":200,"message":"success","token":"xxx"}
```

如果是 HTTPS，通常看不到明文内容。

---

## 十三、HTTP 与 HTTPS 的区别

Wireshark 可以直接看到 HTTP 明文内容。

但 HTTPS 默认是加密的，Wireshark 一般只能看到：

```text
IP
端口
TLS 握手
证书信息
数据包长度
是否重传
是否断开连接
```

通常看不到：

```text
请求路径
请求 Body
响应 JSON
Token
Cookie
```

如果要看 HTTPS 明文接口内容，更适合使用：

```text
Charles
Proxyman
浏览器 DevTools
Postman
```

所以分工是：

| 任务 | 推荐工具 |
|---|---|
| 接口测试 | Postman / Apifox |
| 前后端联调 | 浏览器 DevTools |
| HTTP/HTTPS 代理抓包 | Charles / Proxyman |
| 网络层抓包分析 | Wireshark |
| 后端异常定位 | Spring Boot 日志 |
| 数据校验 | MySQL |

---

## 十四、VPN 对 Wireshark 的影响

如果开启 VPN / Clash / TUN 模式，Wireshark 里可能出现很多：

```text
utun0
utun1
utun2
utun3
```

这些通常是 macOS 上 VPN、代理、隧道产生的虚拟网卡。

VPN 可能导致：

```text
1. 流量不完全走 Wi-Fi: en0
2. 部分流量走 utun 接口
3. 抓到的是加密隧道流量
4. 不容易直接看到目标请求
```

如果只是测试 Wireshark 是否正常，建议先关闭 VPN。

如果是抓本地项目：

```text
localhost:8080
```

一般选择：

```text
Loopback: lo0
```

VPN 影响较小。

---

## 十五、抓包文件保存与安全

Wireshark 抓包文件通常保存为：

```text
.pcapng
```

注意：

```text
不要随便上传抓包文件到 GitHub、论坛、网盘
```

因为里面可能包含：

```text
1. IP 地址
2. 接口路径
3. Token
4. Cookie
5. 请求参数
6. 账号信息
7. 局域网设备信息
```

如果只是练习，抓完可以不保存。

如果用于测试项目记录，可以保存一份，但要注意脱敏。

---

## 十六、毕设测试项目中的使用方式

不要写：

```text
使用 Wireshark 进行接口测试
```

这句话不专业。

应该写：

```text
使用 Postman 对核心接口进行功能测试，并结合 Wireshark 对异常请求进行抓包分析，辅助定位请求链路、TCP 连接状态和服务端响应情况。
```

更完整的描述：

```text
基于毕设系统设计接口测试用例，使用 Postman 构建接口集合并配置环境变量和 JWT Token；通过 MySQL 校验接口执行后的数据一致性；针对接口超时、连接失败等问题，结合 Wireshark 抓包分析请求链路，辅助判断问题位于前端、网络层还是后端服务。
```

---

## 十七、标准排查案例模板

### 问题现象

```text
前端点击登录按钮后提示“网络异常”。
```

### 排查步骤

```text
1. 使用浏览器 DevTools 查看 Network，请求是否发出。
2. 使用 Postman 单独请求登录接口，确认接口是否可访问。
3. 使用 Wireshark 选择 Loopback: lo0 或 Wi-Fi: en0 抓包。
4. 使用 tcp.port == 8080 过滤后端接口流量。
5. 观察是否存在 TCP 连接建立、HTTP 请求发送、服务端响应。
6. 结合 Spring Boot 后端日志定位异常原因。
```

### 判断逻辑

```text
如果 Wireshark 中无请求包：
说明请求可能没有真正发出，优先检查前端 baseURL、代理配置、请求路径。

如果 Wireshark 中有请求但无响应：
说明请求发出但服务端没有响应，优先检查后端服务是否启动、端口是否开放、防火墙或安全组。

如果 Wireshark 中有 500 响应：
说明请求到达后端，但后端代码异常，优先查看 Spring Boot 日志。

如果 Wireshark 中有 401 / 403：
优先检查 Token、权限、登录状态、请求头。
```

---

## 十八、当前阶段必须掌握的内容

最低掌握目标：

```text
1. 会打开 Wireshark
2. 会选择 Wi-Fi: en0
3. 会选择 Loopback: lo0
4. 会开始抓包
5. 会停止抓包
6. 会用 tcp.port == 8080 过滤后端流量
7. 会用 dns 过滤 DNS 流量
8. 会用 http.request 看 HTTP 请求
9. 会用 http.response 看 HTTP 响应
10. 会使用 Follow TCP Stream
11. 会保存 .pcapng 文件
12. 知道不要随便上传抓包文件
```

---

## 十九、当前阶段不要深入的内容

暂时不要花太多时间研究：

```text
1. TCP 深度拥塞控制
2. TLS 解密
3. 高级过滤表达式
4. 网络安全攻击分析
5. 无线网卡监听模式
6. 抓别人设备流量
7. 复杂协议逆向
```

这些会严重偏离当前目标。

当前目标是：

```text
服务毕设项目测试经验
提升接口异常排查能力
为测试岗 / 后端岗增加项目可信度
```

---

## 二十、我的工具组合

当前阶段推荐工具组合：

```text
Postman：接口测试
浏览器 DevTools：前端请求排查
Wireshark：网络抓包辅助定位
MySQL / TablePlus：数据校验
Spring Boot 日志：后端异常定位
JMeter：简单性能测试
```

优先级：

```text
1. Postman
2. 测试用例设计
3. MySQL 数据校验
4. 后端日志排查
5. 浏览器 Network
6. JMeter 简单压测
7. Wireshark 抓包辅助
```

Wireshark 是加分项，不是主线。

---

## 二十一、常用过滤器速查表

| 过滤器 | 作用 |
|---|---|
| `tcp` | 只看 TCP |
| `http` | 只看 HTTP |
| `http.request` | 只看 HTTP 请求 |
| `http.response` | 只看 HTTP 响应 |
| `dns` | 只看 DNS |
| `tls` | 只看 TLS / HTTPS 握手 |
| `tcp.port == 8080` | 只看 8080 端口 |
| `tcp.port == 8080 || tcp.port == 5173` | 同时看 8080 和 5173 端口 |
| `tcp.port == 8080 && http` | 只看 8080 端口的 HTTP 流量 |
| `ip.addr == 192.168.1.10` | 只看某个 IP 的流量 |

---

## 二十二、最终结论

Wireshark 在我的学习和项目中的定位：

```text
不是接口测试主力工具
不是用来替代 Postman
不是用来长期后台运行
不是所有问题都要用它排查
```

正确定位：

```text
Wireshark 是网络抓包和协议分析工具
用于辅助判断请求链路和网络连接状态
适合放在毕设测试项目中作为异常定位加分项
```

最实用的使用场景：

```text
1. 判断请求有没有真正发出去
2. 判断 TCP 连接有没有建立
3. 判断服务器有没有响应
4. 判断是否存在重传、超时
5. 判断 DNS 是否正常解析
6. 辅助区分问题在前端、网络层还是后端
```