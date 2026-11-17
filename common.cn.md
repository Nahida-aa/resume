## 用于提交的简历

1. 没有交社保 不算 工作经历
2. 不建议写至今

### 个人优势

AI算法、DevEx、全栈

1. wakatime 记录 coding 6666+ 小时
2. 日常使用 Rust、TypeScript，Python, Arch Linux, C++
3. **Rust**：写得了底层实现，也做得了工程基建——
   自研 fnrpc（零分配中间件、端到端类型安全）、
   aa-workflow（仅追加事件日志、策略中间件化）、
   aa-session（直读多种异构会话存储），
   熟悉 axum / tower / burn / ort 
4. **TypeScript**：tanstack、solid、orpc、hono、
   drizzle、bun，能靠类型编程写库
   （agnostic-query 核心是类型定义，可整体摇树）
5. 把 Rust 应用编译到 wasm 在浏览器运行（GPUI / Trunk），
   处理多线程、共享内存与 COOP/COEP 隔离
6. AI coding 1000+ 小时
7. **开源贡献**：
   - gradio-rs#11：修复 Gradio 6.x i18n label
     反序列化兼容，含回归测试与线上 app 验证
   - Zed#66120: agent 流式 tool call 修复, 含回归测试
     
### 求职期望

1. 算法工程师 15-30k
2. 其他后端开发 15-30k
3. 高性能计算工程师 15-30k

### 工作经历

#### 一

##### 公司名称

菲克斯哈尔特重构交互网络（保定）有限公司

##### 行业

互联网

##### 在职时间

2025.07-2026.06

##### 职位名称

联合发起人

##### 工作内容

面向 Minecraft 资源创作与协作的平台：项目（作品）可发布多个版本
并接受审核，支持团队与联合创作（发起 → 合同）。

1. 制定模块划分与文件命名规范：约束单文件规模（300 行黄牌 /
   500 行红牌）与导出数量（5–7），业务按 domain 拆分到 module
   目录，统一 constants / tables / schemas / api 四类文件的命名与位置。
2. 编写平台需求文档：定义项目／团队／社群／用户四类实体、
   归属关系、默认频道规则，以及联合创作的两段状态流转
   （发起 → 合同）。
3. 实现认证与会话管理、频道与私聊数据模型、项目成员邀请与
   通知流程；设计客户端直传对象存储的上传链路
   （预签名 → 直传 → 创建版本），版本创建与审核状态挂钩。
4. 先以 Next.js 单体组织业务，后迁移为 bun + turbo monorepo
   （web / api / cdn + shared 层），管理后台独立成仓。
5. 实现压缩包元信息推断：按 jar/zip 内的元数据文件
   （neoforge.mods.toml、mods.toml、fabric.mod.json、mcmod.info、
   pack.mcmeta）判定项目类型（mod / 资源包 / 数据包）、Loader、
   Minecraft 版本与版本号；解析失败时回退到文件名匹配，
   并处理 ${file.jarVersion} 占位符（从 MANIFEST.MF 取实际版本）、
   NeoForge 的 Maven 版本区间到 MC 版本的换算。
6. 发布流程：先用 Docker 打包，以 nginx upstream 切换实现蓝绿，
   后改为 pm2 双实例按就绪信号优雅更新；编写部署手册。
7. 建立 coding agent 协作约定：根与子包各一份 AGENTS.md，
   约定禁止类型断言（as / satisfies / 手写泛型参数）、
   泛型参数统一 T 前缀、schema 作为类型唯一来源，
   并规定每次变更后跑类型检查；配套三个按包加载的本地 skill
   （drizzle 数据建模、drizzle-orm、TanStack DB 数据源选型）。
8. 阶段：已上线运行；合同签署环节未完全实现。

##### 工作业绩

项目中客户端用 TanStack DB、服务端用 Drizzle 各写一遍
筛选逻辑，两份表示的语义需要手工保持一致；这一需求后来独立为
agnostic-query 库，以查询 schema 作为中间表示在两侧双向转换

##### 拥有技能

tanstack, bun, nextjs, orpc, postgres, drizzle, hono

### 项目经历

#### LocalDub

##### 担任角色

主要开发者

##### 项目时间

2026.05-2026.11

##### 项目描述

本地视频翻译配音工作流，个人电脑上总实时率 < 1。
支持错误恢复、人工干预、重跑部分步骤。

步骤（除"翻译"外均可选）：
1. 人声 / 背景声分离
2. 语音识别
3. 字幕关键帧识别
4. 字幕文字光学识别
5. LLM 修复识别结果
6. LLM 翻译字幕为目标语言（必经步骤）
7. 语音克隆
8. 混合各步骤结果为最终视频

##### 项目业绩

- **语音链路**（人声分离 / 语音识别 / 语音克隆）由 vox-lab 承接：
  - 多后端选型：同一模型横比 Rust（Burn）/ C++（whisper.cpp、
    demucs.cpp，GGML）/ PyTorch / 云端在 CPU / Vulkan / ROCm 上的
    表现，定位瓶颈在通用栈而非硬件——同机核显上 whisper Vulkan
    RTF 0.081 远快于 CPU，推翻「核显 Vulkan 必慢」
  - 落地与验证：选定 WGSL/Vulkan 路径后分离 RTF 0.11（tch 基线
    1.03，约 9×），用 corr 1.0000 与上游 CLI 逐样本一致（maxdiff 0）
    验证数值可信
  - 缺陷定位：追出 cubecl SPIR-V 后端 bf16 elementwise multiply
    有误（silu(23.75)≈3616、10*2=2560）并写探针复现，改用 f16
    （同 2 字节、容量不变）绕过
  - 结果可靠性：记录 ROCm 25/25 SIGSEGV、HIPBLAS hang、缺
    gfx1103 Tensile 内核等失败路径；验证 GPU Hang 会连锁污染
    后续进程，实验改为每 trial 独立子进程
- **视觉链路**（字幕关键帧识别 / 字幕 OCR）由 vision-lab 承接：
  - 跨语言横比：字幕 OCR 建 cpp / py / rust 三实现基准，
    同权重同线程下发现差距集中在宿主侧（numpy 归一化每帧
    新建 ~115MB 临时数组、det→rec 首帧惩罚），而非推理内核——
    三者最终调用同一份 C++ 内核，据此把优化落在预处理与
    调用方式上（单帧 442→365ms）
  - 正确性对齐：Rust 复刻 C++ 版 VideoSubFinder，逐帧比对到
    段边界差 ≤1ms（4 段差异仅原版段尾固有 1 帧），并反查
    出原版 cli 分支的帧同步 bug 与 align 默认值差异
  - 设备加速：把 OCR 换到 DirectML 后 355→154ms/帧（RTF 0.309），
    并验证输出与 CPU 后端逐位一致才采用
- 从中抽出两个可复用的 Rust 库（可复用性排序）：
  1. aa_workflow_core：持久执行引擎，核心是仅追加事件日志，
     凭已持久化的日志跳过已运行的步骤
  2. fnrpc：用属性宏在编译期收集函数类型信息，
     生成路由 schema 与 TS 类型

##### 项目链接

https://github.com/Nahida-aa/LocalDub

#### vox-lab

##### 担任角色

主要开发者

##### 项目时间

2026.07-2026.11

##### 项目描述

语音处理多后端实验与 Rust 化工作区，覆盖 ASR（whisper）、
音源分离（demucs）、TTS（VoxCPM / CosyVoice），
横向实测 Rust（Burn / ort）、C++（whisper.cpp / demucs.cpp，
GGML 与 ONNX Runtime 后端）、PyTorch 与云端在
CPU / Vulkan / ROCm 等设备上的推理表现。
同一套底层能力（模型加载、权重 dtype 泵、
后端抽象、基准与数值校验）支撑，故放在同一工作区。
为 LocalDub 的语音步骤提供选型与实现，但它本身是
覆盖全链路的独立实验线，不是某一环节的附属。

##### 项目业绩

- **音源分离选型**：同一模型横比 6 种后端，定位瓶颈在通用栈
  而非硬件（同机核显上 whisper Vulkan RTF 0.081，远快于 CPU，
  推翻"核显 Vulkan 必慢"），选定自研 WGSL/Vulkan 路径后
  RTF 0.11（tch 基线 1.03，约 9×）；用 corr 1.0000、
  与上游 CLI 逐样本一致（maxdiff 0）验证数值可信
- **TTS 后端**：定位 cubecl Vulkan/SPIR-V 后端
  bf16 elementwise multiply 有误（silu(23.75)≈3616、
  10*2=2560），写探针复现；改用 f16（同 2 字节、容量不变）
  后 Vulkan RTF 3.9–5.6，优于 PyTorch CPU 的 8.29
- **推理链路确定性**：给 CFM 初始噪声与 VAE NoiseBlock 注入
  可复现 seed（手写 Box–Muller 对齐 N(0,1)），
  使同一文本可稳定复现波形、可做回归测试
- **并行与取舍**：实现 batched prefill / 并行片段
  （per-batch 真实长度、padded 输入按索引取 last hidden state，
  无 batch 时走原串行路径）；按分段耗时
  （LM 46.6% / CFM+VAE+encoder 52.6%）决定只把 53% 搬 GPU、
  LM 留 CPU，避免全栈重写
- **失败路径记录**：openai-whisper + ROCm 25/25 SIGSEGV
  （连 tiny 也崩）、whisper.cpp HIPBLAS MES hang、
  PyTorch ROCm 缺 gfx1103 Tensile 内核；并验证 GPU Hang
  会连锁污染后续进程，实验改为每 trial 独立子进程

##### 项目链接

https://github.com/Nahida-aa/vox-lab

#### vision-lab

##### 担任角色

主要开发者

##### 项目时间

2026.07-2026.08

##### 项目描述

视觉方向的实验工作区，三个强相关目标共用底层 OCR 引擎
与抓图/注入设施：视频字幕识别（核心）、GUI 自动化测试、
GUI 智能操作。

##### 项目业绩

- **字幕 OCR 的 Rust 化**：从 Python（rapidocr-onnxruntime）
  迁到 Rust（ort-rs + opencv-rs）常驻推理——同权重（PP-OCRv4）、
  同 4 线程下单帧 442ms → 365ms（约 1.2×）；相对原先每帧起
  进程、重建 3 个 ORT session 的调用方式，827ms → 365ms（约 2.3×）。
  拆解确认收益来自宿主侧开销（numpy 临时数组、det→rec 首帧惩罚、
  arena 配置差）与进程模型，不是语言本身
- **三实现横比基准**：cpp / py / rust 三者同权重、同线程、
  同帧对比；Rust 侧 CER(paired) 0.18%、逐位可复现，
  warp 版与原 C++ 实现逐位一致（CER 0.36%、0 missed）
- **字幕关键帧识别的 Rust 复刻**：复刻 C++ 版 VideoSubFinder，
  段边界与原版对齐（差 ≤1ms，4 段差异仅 C++ 段尾固有 1 帧），
  并排查出 C++ 侧的帧同步 bug；性能与 C++ 相当
- **多后端性能验证**：OCR 从 C++ 355ms/帧降到 Rust+DirectML
  154ms/帧（RTF 0.31），输出与 CPU 后端逐位一致

##### 项目链接

https://github.com/Nahida-aa/vision-lab

#### aa-session

##### 担任角色

主要开发者

##### 项目时间

2026.10-2026.11

##### 项目描述

把本地 agent 会话（opencode / codebuddy / claude / zed ...）
导出成可以直接在编辑器里全文检索的 JSON / Markdown。
四种来源各自直读自己的存储格式（SQLite / JSONL /
zstd 压缩 blob），互不共享代码；新增数据源
只需实现一个 SessionSource。

**诞生原因**：zed 原版（1.23.x / 1.24.x）的 agent
在流式响应里会持久化空的 `tool_call` id / name，
之后每一轮请求回放这段历史都会被 provider 直接 400，
整个会话永久卡死。要排查就得能直读、导出、改完再写回
会话历史——手边没有这样的工具，于是写了 aa-session。

##### 项目业绩

1. **主动砍掉自建全文索引**：维护 SQLite FTS5 侧车
   带来 470MB 重复数据、索引永远可能过期、还要管
   schema 版本；最终把检索交还给用户编辑器，
   自己只负责导出成可读文本
2. 子会话识别：本机 891 个会话里 652 个是子会话
   （其中 607 个是 explore），默认折叠，避免列表被淹没
3. **定位并上报 zed 的会话损坏 bug**：三种形态
   （id 与 name 都空 / 只有 id 空 / arguments 丢失）
   分别对应不同的恢复手法（伪造一致的 id、裁剪末条消息、
   改 arguments），并提交了上游 issue zed#65655，
   定位到 `chat_completion.rs` 的 drain 未过滤空 id/name
4. 批量导出强制要求筛选条件，防止手滑把几十 GB
   写进当前目录；单条失败不中断整批，最后以
   非零退出码报告失败数
5. 输出格式 toon：表头只写一次键名，
   30 条会话实测比 JSON 省 48% token

##### 项目链接

https://github.com/Nahida-aa/aa-session

#### fnrpc

##### 担任角色

主要开发者

##### 项目时间

2026.06-2026.07

##### 项目描述

Rust + TypeScript 全栈类型安全 RPC 框架。

**核心机制**：属性宏在编译期收集函数的参数、返回值等
类型信息，据此生成路由 schema 与对应的 TS 类型——
Rust 里定义一次 RPC 函数，前端调用全程类型安全，
无需手写接口定义或二次生成步骤。

**其余机制**：
- 三种过程宏：`rpc_query` / `rpc_mutate` / `rpc_subscribe`，
  统一处理单参数、多参数与上下文注入
- 双运行时：同一套路由既走 Axum（HTTP）也走 Tauri（IPC）
- 订阅：SSE（Axum）或 `Channel<string>`（Tauri），
  前端以 `AsyncIterable` 消费
- 分层中间件：`HookLayer`（前/后钩子）、`TracingLayer`（日志）、
  自定义 `FnLayer`，链路内用 `&[u8]` 传递避免重复序列化
- BigInt 安全：传输层统一处理 BigInt 序列化
- 提供 `@fnrpc/client` 与 `@fnrpc/tanstack-query`
  （query / mutation / stream / live）

**两种用法**：fnrpc 生成的路由既能挂到其他框架上
（内置 Axum / xitca-web / Tauri 桥接，未内置的也能自己写），
也能直接与 `fnrpc-web`（自带的服务端运行时）搭配使用。

##### 项目业绩

- **零擦除中间件**：基于 RPIT，无 `#[async_trait]`，
  未挂中间件时不产生 `Box::pin`
- 中间件链用 `&[u8]` 而非 serde `Value`，
  省掉整条链的序列化开销
- 分配实测（dhat，每 1000 次调用）：
  fnrpc-web 845B / xitca-web 1048B / axum 2373B，
  比 axum 少约 2.8 倍分配；无业务逻辑路径低至 96B
- 属性宏在编译期提取函数类型信息，同时产出路由 schema
  与 TS 类型，避免类型定义与实现两处维护、容易漂移
- 从 LocalDub 中独立出来，降低参与 LocalDub 开发的门槛

##### 项目链接

https://github.com/axdx-lab/fnrpc

#### aa-workflow

##### 担任角色

主要开发者

##### 项目时间

2026.09-2026.11

##### 项目描述

耐久执行引擎与工作流编排引擎。可编排多步流程、
编排多个 agent，或作为单次 agent 循环。

架构分两层：

- **引擎（core）**：只提供机制——耐久执行、仅追加事件日志、
  中间件（洋葱模型），本身不含任何调度策略
- **策略层**：以中间件形式挂在引擎上，
  目前三个策略都做成了中间件：
  - `dag`：以声明图接管 handler
  - `reseed`：给一份"要重跑"的 step 名单，
    名单内的重新执行，名单外的直接拿上一次 run 的结果
  - `interim_goals`：临时目标，目标齐了就泊车

引擎不认识 DAG，也不知道什么叫"重跑"或"目标"。
中间件只做两件事：**在处理器前后插入逻辑**、
**修改 ctx**——仅此而已。

策略之所以能做出来，是因为每次 step 调用都能被
包一层：中间件在 step 外再套一层，决定是继续往下
执行还是直接返回旧结果。多层叠加时先注册的在外层，
谁说了算由嵌套顺序决定，不会互相覆盖。

##### 项目业绩

- 让 LocalDub 的部分步骤按声明自动并行，提升整体性能
- 编排方式规范化：直接看得到声明的 DAG，
  降低代码阅读难度，步骤间依赖关系更清晰
- DAG 作为中间件挂在引擎上：由形状 + 源 run 日志折算
  重跑失效名单，某个步骤失败修复后只重跑它及其下游，
  其余步骤的 checkpoint 直接复用
- 机制与策略分离：调度策略一律走中间件，
  引擎不内置任何策略参数

##### 项目链接

https://github.com/Nahida-aa/aa_workflow

#### agnostic-query

##### 担任角色

主要开发者

##### 项目时间

2026.02-2026.2

##### 项目描述

运行时无关、数据库无关、可跨网络传输的查询协议
（QuerySchema）。

**为什么做**：用 TanStack DB 时，同一套筛选/排序
要在客户端写一遍 DSL，到服务端再用 Drizzle 写一遍，
重复且容易对不上。

**做了什么**：
- 一份纯数据描述 where / orderBy / limit / offset，
  可在 TanStack DB、Drizzle、Kysely、原生 SQL 之间
  双向转换，并提供 zod / valibot 校验器
- 字段路径用元组表示，支持 JSONB 与数组索引，
  编译期做类型检查
- 可从零构建，也可基于已有 schema 增量修改
  （比如追加权限过滤）

**为什么只有 TS**：TS 的类型编程能力支撑了这套
API 体验，其他语言难以做到一致；Rust 等语言的
实现思路已有，但暂未落地。

##### 项目业绩

1. 已发布并被 TanStack 生态的外部项目使用
2. 处理过来自其他开发者的 issue：
   - 子路径 exports 解析失败（moduleResolution
     为 bundler / node16 / nodenext 时全挂）
   - SQLite 适配器误引用 PG 的字段格式化，
     导致 JSON 路径生成 `->>` 而不是 `json_extract`
3. 部分函数还可用于 tanstack db 生态的库开发，
   例如自定义收集器、解析 TanStack DB 查询
   语法树为 JSON、构建语法树的测试演示
4. 零运行时依赖，核心是类型定义而非逻辑，
   整体可摇树

##### 项目链接

https://github.com/axdx-lab/agnostic-query

#### 视频翻译配音承接（个体工商户）

##### 担任角色

自雇

##### 项目时间

2026.06-2026.11

##### 项目描述

以个体工商户主体承接视频翻译配音业务，用自研的
LocalDub 工作流完成人声分离 / 背景声分离、语音识别、
字幕识别与翻译、语音克隆、视频合成的全流程，
本地运行，不依赖第三方翻译服务。

##### 项目业绩

- 将自研工作流用于实际承接的业务，验证了整条链路
  在真实素材上可跑通（本地实时率 < 1）
- 自动翻译质量收到用户正面评价

##### 与项目经历的关系

本项目是把 LocalDub 用于实际业务的一次尝试，
工程实现见上方「LocalDub」条目。

### 教育经历

#### 学历

全日制本科

#### 学校

武汉科技大学

#### 专业

卫生检验与检疫

#### 时间段

2021-2025

#### 毕业设计（本科）

**血小板/高密度脂蛋白胆固醇比的变化与中老年人心血管疾病的相关性：
一项全国性队列研究**

- 数据：CHARLS 全国队列（2011-2020）
- 方法：用 K-means 把 PHR 的纵向变化分成 3 类轨迹
  （低稳定型 / 高下降型 / 中度上升型），
  再用多变量 Logistic 回归、受限三次样条（RCS）、
  ROC/AUC 分析轨迹与 CVD 风险的关联
- 样本：从 11,847 名血检参与者中纳入 3,642 人
- 结果：PHR 上升轨迹组 CVD 风险显著升高
  （OR=1.90，95%CI 1.55–2.33）；
  累积 PHR 对 CVD 的预测 AUC 为 0.545

#### 在校经历

1. 全国大学生数学竞赛 国家级三等奖（2023.11）
2. 数学建模竞赛：小队中负责建模与编程
   （2024.5，用 K-means + FFT 从 98 万条
   位置-时间数据推算路口红绿灯周期）
3. GAN / cGAN 实践（PyTorch，2024.11）
   - 自写生成器与判别器、对抗训练循环、条件生成
   - 支持训练中断后继续训练；带 streamlit webui
   - 仓库：https://github.com/Nahida-aa/GAN
   - 权重暂存云盘，未放仓库
4. 个人博客（2022.10月起）：技术栈历经
   Django → Vue + FastAPI → Next.js → Astro；
   现为 Astro + Markdown 的极简形态
5. 高等数学(4.0/4.0 满绩)、计算机基础、计算机网络、
   线性代数、计算机组成原理、概率与统计、离散数学
6. 自写 python, cpp服务端框架实践

## 额外的提交信息
> 简历上可能没有, 但是简历信息收集表格上需要的

## 待修改的讨论

### 1. Apifox 岗位

- **投递动机**：刷到 JD 时发现它偏 DevEx。
  投的是 DevEx 这个方向，和规划中的 Zed 属于同一类，
  不是冲着 Apifox 这个产品去的。
- **放弃原因**：HR 已读不回，说明对方没看懂我的水平
  （800 小时 ai coding 正是这个 JD 的硬性门槛，
  却没有被识别）。加上我本身不使用这类产品。
- **调薪资是事后动作**，不是"因为调薪才放弃"：
  期望从 10k-15k 调到 15k-30k，发生在放弃之后。
- **调薪的起因是一个假设**：我问 deepseek，
  它说期望写太低、低于经历身价，
  可能被当成经历造假、没有被认真分析。
  这是推测，不是已证实的原因。
  实际先后顺序是：HR 已读不回 → 我调薪资。
- 我最初的简历规范里**没有 DevEx 这个标签**，
  是后来才补上的。
- Apifox 的核心是 API 研发工具/协同平台，
  我更关注它代表的"工具型 DevEx"方向，
  而不是 Apifox 产品本身。

### 2. LocalDub 的核心

- LocalDub 的核心**不是 fnrpc，也不是 aa-workflow**。
- fnrpc 和 aa-workflow 都是从 LocalDub 中
  **逐渐独立/剥离**出来的能力模块，
  方便复用和降低参与门槛。
- LocalDub 真正的核心是视频翻译配音工作流，
  包含人声/背景声分离、语音识别、字幕 OCR、
  LLM 翻译、语音克隆、视频合成等步骤。
- fnrpc / aa-workflow / vision-lab / vox-lab
  都服务于这个核心工作流。
- **简历里有，话术里没有**：
  boss 直聘上对方能看到完整在线简历，
  项目经历里 LocalDub 本来就在。
  但 10/30 聊天里说的话术全是
  "长期维护自研的 RPC 框架、工作流引擎"、
  "模型推理部署（burn/ort/ggml）和工作流编排"，
  LocalDub 一次都没出现在话术里。
- 所以这不是可见性问题，是**引导顺序**问题：
  对方会顺着我的话术去翻，第一落点是
  fnrpc 和 aa-workflow，LocalDub 要靠自己翻到。
  如果要让人先看到产品，
  就得在话术里提一句它来自 LocalDub。

### 3. 本地实验室项目

- 毕业后没有进行模型训练，不是因为不想做，
  而是因为**经济压力**：父母生病，
  没有足够的钱继续在学校做训练。
- 我后续还会考虑读硕士。
- 我的目标链条（学习是实现一切的前置条件，
  永生/人脑控制/读硕士都是为了保障学习时间）：
  1. 前置条件：**学习**
     （没有足够时间学习是核心矛盾，
     所以需要其他目标来保障）
  2. 目标之一：**永生**
     （为了有更多时间学习，不是终极目标）
  3. 目标之一：**人脑直接控制**
     （手或声音操控都不够好）
  4. 目标之一：**读硕士**
  5. 更近目标：**找一份合适的工作增加收入**
     （收入用来支撑学习和研究时间）
- 逻辑：学习 → 需要时间/资金 →
  永生保障时间，工作保障资金，
  读硕士/人脑控制是方向性目标。
- **Zed 不在链条里，不是必经项**，只是一个好的选择：
  - 福利很吸引人：无限带薪休假
    （如果我开公司，我也会提供无限带薪休假）
  - 现代 DX 已经包含 AX 了，因为 agent 也是开发者
  - Zed 团队现在也主要在往 AI 方向做
  - 所以 Zed 和"AI 作为主线"是相容的，不必二选一
- 本地 lab 项目：
  - `/home/aa/repos/ai_ls/vision-lab`
  - `/home/aa/repos/ai_ls/vox-lab`
- 这两个项目后续可能作为简历补充材料，
  但优先级低于 Online 上能直接展示的项目。

### 4. 时间线确认（已确认不重要）

- Fixheart：2025.07-2026.05，**全职**。
- 大余县恒耀广告工作室：2026.06-至今，
  **兼职/个体户性质**。
- boss 直聘没有"是否全职"字段，
  所以在线简历上只能写时间段，不能标注兼职。
- 原本担心 HR 看到 2026.06-至今 会误以为
  刚毕业就创业、经历不连贯。
- **这条不重要**，不用再纠结怎么写：
  10/29-10/31 三天的真实沟通里，
  没有任何一个 HR 问过全职/兼职，
  也没有人问这段经历的连续性。
  所有人都在问技术栈、方向匹配、薪资和城市。

### 5. 定位主线：AI 为主，DevEx 为辅

- 原先把 DevEx 当主线，AI 是旁支。
- 现在发现做 AI 相关内容也能有收入，
  开始把偏 AI 的那部分捡起来：
  **AI 模型相关的底层**，不是工程底层，
  也不是工程体验方面。
- 我本身喜欢 AI 和数学。
- 所以可以定成：**AI 作为主线，DevEx 作为其次**。
- 这也解释了为什么 10/29-10/31 三天
  对外的自我描述重心全在底层 / AI Infra 上：
  那不是临时话术，是真实的主线在挪。

### 6. 简历问题清单（全量，带状态）

**说明**：这是"目前已经发现的点"的汇总，
不代表清单外没问题；也不代表列进来的都要改。
状态标记：✅ 已改 / ⏳ 待定 / ✖ 不重要 / ❓ 需确认

#### 一、自我介绍没有量化，反而显得不可信 ✅

- 原来是一条长句，1 万小时 + 1000 小时 AI coding
  这类数字没有出处，容易被当成注水。
- 已改：拆成定位句 + 若干条可验证事实，
  时间改用 wakatime 统计口径（6666+ / 800+）。

#### 二、最强的项目放在最后 ✅

- 原顺序 aa-workflow → fnrpc → LocalDub → agnostic-query，
  王牌 LocalDub 排在倒数第二。
- 已改：LocalDub 打头，且首句给结论
  （"个人电脑上总实时率 < 1"）。

#### 三、Fixheart 那段写得像 CRUD 后台 ✅

- 删掉了"实际上老板想让我当 CTO"（移入第 7 条）。
- 补了"从 0 到 1 独立负责"、WebSocket、
  以及孕育出 agnostic-query 这层关系。
- 难点不展开（时间久远，追问会尴尬），
  复盘答法见第 7 条。

#### 四、工作时间和项目时间大面积重叠 ✖

- 判定为不重要：LocalDub 是自用产品，
  fnrpc / aa-workflow 是它剥离出的库，都会持续维护，
  "至今"是正常状态，不是并行做三个项目。
- boss 的时间字段是日期选择器，填不了括号说明，
  已全部清理为纯日期。

#### 五、教育经历里最硬的一条被埋了 ✅

- 毕设已提到在校经历之前，单独成节。
- 在校经历补了数学建模的具体数据（98 万条位置-时间）。

#### 六、错别字 ✅

- `LoaclDub` → `LocalDub`
- 项目名 `agnonstic-query` → `agnostic-query`（含链接）

#### 七、一个文件混了两样东西 ⏳

- "在线简历内容"和"待修改的讨论"混在同一个文件。
- 这不是简历问题，是文件组织问题。
- 待定：将来如果要给别人看简历，可以拆成两个文件。

#### 八、Rust vs C++ 的性能口径 ⏳

- 详见第 8 条，等另一个 agent 的结论。

#### 九、个人优势和项目经历内容重复 ⏳

- 个人优势写了"C++ 状态机重写为 Rust，速度 +40%"，
  LocalDub 的项目业绩又写了一遍。
- 同一件事读两遍，浪费版面。
- 待定：是让个人优势只留"能力标签"，
  还是让项目业绩只留数字。

#### 十、语言定位前后不一致 ⏳

- 简历现在写"Rust 与 TypeScript 双主力，Python 辅助"。
- 但 10/30 对华为 OD/比特鹰说的是
  "主力语言是 Rust、C++ 和 Python"。
- 待定：统一口径，并配套改对外话术。

#### 十一、缺一句"我能解决什么问题" ⏳

- 现在通篇是"我会什么、我做过什么"，
  没有一句面向雇主的"我能帮你做什么"。
- 投不同公司时，这句话可能是最重要的差异点。
- 需要用户参与，不能替他写。

#### 十二、common.cn.md 与 10/28 版本已经不一致 ⏳

- 10/28 这份改了很多，common.cn.md 没动。
- 将来投递时可能投错版本。
- 待定：以哪份为准，另一份是否废弃或同步。

#### 十三、求职期望还是 todo ⏳

- 需要填城市、薪资、岗位方向。
- 目前对外说的是"广州/深圳优先，东莞可接受，
  期望 16-20K"，可以直接用。

#### 十四、缺少可量化的外部认可 ⏳

- 除了 GitHub 和 wakatime，没有 star 数、
  npm 下载量、issue 数这类可查的指标。
- agnostic-query 有真实 issue（#20、#32），
  npm 下载量需要用户提供。

### 7. 本地额外信息（不直接写到简历上的信息）

#### Fixheart 的真实情况

- 表面上职位写的是"全栈工程师"。
- 实际上老板想让我当 CTO。
- 这条**不写进简历**：写出来只会让人问"那为什么没当上"，
  不会加分。面试如果聊到职责范围，可以口头说明
  实际承担的是技术决策和架构职责。

#### effect-ts 的定位

- 知道它最近在 TS 圈很火，但**还没用过**。
- 所以简历和技术话术里不要把它算进"已涉猎"的清单，
  否则被追问会露怯。
- 如果对方问起，如实说"还没用过，但知道它解决什么问题"。

#### 笔试/算法题用 Python 或 Rust，不代表 Python 更熟

- 算法竞赛和机考时只选 Python 或 Rust
  （国内大多数平台一般只有 Python/C++/Java/Go，
  所以实际多半选 Python）。
- 选 Python 的理由是**方便快速默写**，
  不是因为对 Python 更熟悉。
- 所以话术上说"Python 辅助"没问题，
  但不要被理解成"Python 是主语言、Rust 只是会用"。
  真实定位是 Rust 与 TS 双主力。
- 10/30 华为 OD 机考已定 11.8，
  当时跟 HR 说的是"计划用 Python 或 C++ 答题"，
  这点可以在机考语言确认时再说清。

#### 时间数据怎么算、怎么写

- **wakatime 上是真实统计值**：coding 6666+ 小时，
  其中 AI coding 800+ 小时。
- 简历/话术里说的"AI coding 1000+ 小时"是**推测**：
  基于 wakatime 的 800+，因为很多时间没被统计
  （尤其是最近用 zed，wakatime 插件不稳定）。
- 两个数字的口径不一样，混用会被人抓：
  - 说"wakatime 统计"就写 6666 / 800
  - 说"实际"就说明是推算，并给出为什么低估
- 不要再用原来的"总 coding 1 万小时"：
  这个数字缺乏来源，是这次被怀疑注水的根源之一。
- 建议在简历里直接写可验证的：
  "wakatime 记录 6666+ 小时，其中 AI 辅助 800+ 小时"
  ——数字更小，但可查、可信。

### 8. Rust vs C++ 性能口径问题（待另一个 agent 研究结论）

#### 现状

- `subtitle_finder` 用 `--profile` 实测：**14.4ms/帧**
  （单 worker，1017 帧视频，本机 16 核）。
- 与 2026-08 基线比：Rust 19.9 → 14.4ms（提速约 28%），
  相对同基线的 C++ 20.5ms 快约 30%。
- 用户记忆是 12-14ms/帧，与实测吻合。

#### 踩过的坑

- 一开始用"墙钟 ÷ 总帧数"算每帧，得出 9.9ms，
  又拿 C++ 同样口径算出 8.8ms，误判成"Rust 比 C++ 慢"。
- 错因：Rust 侧解码单独耗时 10.0s，墙钟大头是解码；
  C++ 侧墙钟也含自己的解码开销，两边口径不同。
- `--profile` 在 16 workers 下显示 38.6ms/帧，
  那是**各 worker 累计的 CPU 时间**，不是墙钟。

#### 简历上要不要写、怎么写

- 可以写，但必须带口径：
  "单 worker、1017 帧视频、本机实测 14.4ms/帧，
  对比 C++ 基线 ~20.5ms/帧"。
- 不要只写"比 C++ 快 40%"——数字区间是 28%~40%，
  取决于拿哪个基线比，单独写 40% 站不稳。
- 更好的写法可能是：**不提"比 C++ 快"，
  只提绝对值和做法**（SIMD 重写热点算子 + 状态机对齐），
  避免被要求现场复现。

#### 待确认（另一个 agent 正在研究）

- +40% / 12-14ms 的原始测量条件是什么
  （哪台机器、哪个视频、哪个版本）。
- C++ 侧能否加阶段级计时，做到严格同口径对比。
- 结论回来后，再定简历里那句话怎么写。

#### Fixheart 的复盘（简短回答用）

- 简历上**不展开难点**：项目时间久远（2025.07-2026.05），
  写太细会被追问细节，而细节已经记不清了。
- 如果被问到"最大的挑战/返工"，可以答：
  1. **数据库设计**推翻重来过一次
  2. **文件组织结构**调整过——当时的做法是让每次
     修改和阅读时要浏览的文件夹尽可能少；
     现在回头看，更合适的做法是做成**一棵分类树**，
     用层级来承载"改动时只看相关部分"这个需求，
     而不是靠扁平目录 + 命名约定
- 这个回答的好处：承认返工、给出具体原因、
  并且说明现在的理解已经变了（体现成长），
  不需要编造细节。

### 9. 在校期间的 Web 项目（未写完，但可能值得写）

这些项目在 common.cn.md 的 Experience 里出现过，
但在 boss 在线简历里没有。状态都是"没做到想要的样子，
因为忙，后续没管"。

#### blog

- 时间：2023.6 - 2024.3（在 common.cn.md 里）
- 技术栈演变：**Django → Vue + Django → Next.js → Astro**
  - 注：不是"Django 换成 Vue"，是 Django 后端 + Vue 前端的阶段
  - 现在是 Astro + Markdown，几乎没有额外功能，
    是"功能最少"的阶段
- 至今没做成想要的样子，主要原因是自己的想法一直在变，
  加上太忙
- 现在的 Astro 版本就是 agnostic-query 文档站用的那套

#### 其他

- AI chatbot
- 一些小工具
- 实时通信相关的练习

#### 简历上怎么处理（待定）

- 价值在于：体现了**技术栈迁移的判断**（从 Django 一路换到 Astro），
  以及"博客"这事从 2023 年一直在弄——即长期在做
- 风险在于：都没有做完，被追问细节会尴尬
- 折中写法：不列成"项目经历"，放在在校经历里一句话，
  例如"个人博客，技术栈历经 Django → Vue → Next.js → Astro 的迁移"
- **能访问，但不希望别人打开**：半成品，
  而且一直在加功能/砍功能之间反复，
  现在的 Astro 版是功能最少的阶段（几乎只有 Astro + Markdown）。
- 所以可能不适合在简历上放链接；
  但"在加功能和砍功能之间反复、最后选择最简方案"
  这件事本身可以是一个故事，前提是对方愿意听。

#### aa-session 的真实状态（不写进简历）

- 仓库公开（github.com/Nahida-aa/aa-session），
  但**暂时没让别人用**。
- 原因：
  - 过于早期，存在 bug
  - 没有考虑操作系统兼容性
  - 有一些功能可能没做完，比如 markdown 导出
    还没验证是否符合预期
- 简历上的处理：
  - 写"做了什么判断、砍掉了什么"，
    不写成熟度、不写用户数
  - 数字（470MB / 891 / 652 / 48%）都是真实测量值，
    可以放心写
- 如果被问到"有人在用吗"：如实说"目前主要自己用，
  还在早期"。不要硬说成熟。

### 10. aa-workflow 现状与简历写法不一致（待定）

#### 检出的事实

- Rust 仓库 `lib_ls/aa-workflow` 现在有 5 个包：
  `workflow_core` / `workflow_runtime` / `workflow_dag` /
  `aa_graph` / `workflow_store_sqlx_postgres`。
  简历里只写了前三个。
- `workflow_dag` 当前的 Rust 实现只做**重跑失效名单计算**
  （`DagShape::reseed_plan`：种子 + 全部下游）。
  **按声明自动生成并行 handler 的部分，Rust 侧还没有。**
- "按 DAG 声明自动并行"这套能力，目前在 TS 实验里验证：
  `lib_ls/learn_ls/wf-demo`
  - dataflow 调度：依赖就绪即触发 `ctx.step`，
    没有波栅栏那种假等待
  - 部分运行：`to` 边界，只跑各终点的祖先闭包
  - 子图重跑：`descendants(from)` 闭包真实执行，
    保留集走 cached 结转
  - 分段重跑：重跑区域 = `descendants(from) \ to 严格后代`
  - `packages/aa-dag` 是 Rust `aa_graph` 的 TS 移植
- README 状态：实验阶段，尚未接回 LocalDub。

#### 简历里那两条现在的问题

1. "让 LocalDub 的部分步骤按声明自动并行，提升整体性能"
   —— Rust 侧还没这个能力，现在写属于夸大。
2. "提供三个主要包" —— 漏了 `aa_graph` 和 Postgres store。

#### 三种写法（待选）

- A. 只写 Rust 现状：声明形状 → 重跑失效名单（种子 + 下游），
     其余步骤 checkpoint 复用
- B. 写完整构想：声明式 DAG + dataflow 调度 + 部分运行 /
     子图重跑 / 分段重跑，注明"TS 侧验证，Rust 侧移植中"
- C. 先不改，等 Rust 侧把生成并行 handler 补上再写

#### 决定时机

用户这几天在准备笔试（华为 OD 机考 11.8），
同时继续写 workflow，等 Rust 侧进展后再定稿。

### 11. wasm 与"提速 40%"的修正（已改：wasm 按方案 A 写入，40% 已删）

> 2026-11-14 定稿：个人优势新增第 5 条
> "把 Rust 应用编译到 wasm 在浏览器运行（GPUI / Trunk）"，
> 采用方案 A；LocalDub 的 wasm 落地仍等接入前端后再补。
> "+40%"那句已从个人优势删除。

#### 一、"速度 +40%" 不成立，要从简历里删掉

- 用户自述：刚发现那个提速**很可能是机器状态带来的错觉**。
- 之前的复测也已经对不上：Rust `--profile` 14.4ms/帧，
  C++ 墙钟 8.2-8.3s / 1017 帧；同机同视频下两边没有可比的速度差。
- 结论：**个人优势里"Rust + 汇编，速度 +40%"整句删除**，
  LocalDub 业绩里对应那条也要改。
- 可以保留的是**做法**而不是数字：
  "用 Rust 复刻 C++ 的 VideoSubFinder 关键帧识别，
  段边界与原版对齐（差 ≤1ms）"
  这类**可验证的正确性结论**，比速度数字稳。

#### 二、wasm 目前只有基础，没有落地

检出的代码事实：

- `LocalDub/packages/bridge`：写了个 `#[export]` 属性宏，
  在 `feature = "wasm"` 下自动挂 `wasm_bindgen`，
  并校验"只能有一个 options struct 参数"。
- `LocalDub/packages/time`：`wasm` feature + `tsify-next`，
  `MediaTime` 上的方法挂了 `#[export]`。
- **但整仓没有找到前端实际调用 wasm 的路径**，
  也就是说这部分是"为 wasm 准备好了"，还没跑起来。

#### 三、真正的 wasm 实践在 gpui 上

- `ide_ls/gpui_learn/apps/_01_hello_world`：
  手写 WASM 入口 + `wasm-bindgen` + bun 静态服务。
- `ide_ls/gpui_learn/apps/_02_hello_web`：
  走官方的 trunk 路线，把 GPUI 应用跑在浏览器里，
  验证了"wasm 多线程 + 出画面"。
- 踩过的坑都记录在 README / TROUBLESHOOTING：
  - `Trunk.toml` 必须大写 T，否则 COEP/COOP 头不生效，
    浏览器不是跨源隔离，`SharedArrayBuffer` 不可用，
    GPUI 退回单线程 dispatcher
  - 不要写 `#![cfg_attr(target_family="wasm", no_main)]`
  - `wasm-bindgen` 必须锁 0.2.120（0.2.121+ 注入
    worker 线程 id 时找不到 `__heap_base`），
    CLI 版本要和 Cargo.lock 一致
  - 需要 `+atomics,+bulk-memory,+mutable-globals` + 
    `--shared-memory` + `--import-memory`，
    并导出 `__wasm_init_tls` / `__tls_size` / `__tls_align` / `__tls_base`
- 这说明对 wasm 的理解是**真的跑通过**，不只是"了解"。

#### 四、简历里怎么写（待选）

- 方案 A：加一条能力描述，不挂到具体项目
  - "把 Rust 应用编译到 wasm 在浏览器运行（GPUI/Trunk），
     处理多线程、共享内存与 COOP/COEP 隔离"
- 方案 B：挂到 LocalDub 上
  - "为 Rust 核心提供 wasm 导出宏（bridge），
     为浏览器侧复用做准备（尚未接入前端）"
  - 风险：容易被追问"用在哪"，目前没有答案
- 方案 C：先不写，等 LocalDub 的 wasm 真正接入前端后再写
- **建议**：A 先上（这是真本事），B 等落地后再改。

#### 五、Dioxus / GPUI 的定位

- 用户了解 Dioxus，且用 GPUI 做过能在浏览器运行的软件。
- 这两个都属于"Rust 写 UI"，和主线（AI 模型底层、DevEx）
  不是同一条线，但能证明**跨端与底层调试能力**。
- 简历上的位置：不进项目经历，放个人优势的一句话，
  或者放进"本地额外信息"等面试时展开。

#### 决定时机

用户这几天在准备华为 OD 机考（11.8），
同时继续写 workflow，等告一段落再定稿。

### 12. 个人优势里的 Rust 那一条太片面（已改）

> 2026-11-14 定稿：个人优势第 3 条改成能力概述
> （fnrpc / aa-workflow / aa-session 并列 + 熟悉组件），
> 不再用单一子任务 + 百分比。"速度 +40%"已删。

#### 现状

```
- Rust 侧：把 C++ 写的关键帧识别状态机重写为 Rust + 汇编，
  质量不变、速度 +40%
```

#### 三个问题

1. **速度数字本身已经站不住**（见第 11 条）：
   用户判断 +40% 可能是机器状态带来的错觉。
2. **以偏概全**：Rust 能力被压缩成"重写过一次状态机"，
   而实际上 fnrpc（零分配中间件、dhat 845B vs axum 2373B）、
   aa-workflow（仅追加日志 + 失效名单）、
   aa-session（直读四种异构存储）都是 Rust 的工作。
3. **写法不像"个人优势"**：个人优势应该是能力概述，
   现在是"某个子任务 + 某个百分比"，读起来像项目业绩。

#### 建议的写法（数字留在项目里，优势只讲能力）

```
- Rust：写得了底层实现，也做得了工程基建——
  自研 RPC 框架（零分配中间件）、工作流引擎
  （仅追加日志 + 失效名单）、复刻 C++ 关键帧识别
  （与原版段边界对齐），并跑通 wasm 多线程
```

#### 待决定

- 去掉速度之后，"复刻 C++ 关键帧识别"还剩
  "段边界与原版对齐（差 ≤1ms）"和"查出 C++ 侧帧同步 bug"，
  这两条是正确性层面的成果，比速度更难做、更难造假。
- 是否保留"并跑通 wasm 多线程"取决于第 11 条里
  wasm 方案选哪一个。

### 13. aa-workflow 的新进展（待补充到简历，待定）

用户近期又迭代了一轮，出现了下面几个方向性变化：

#### 一、很多能力可以用中间件表达

- 原先散落在各处的逻辑，现在发现可以收敛成
  **中间件**统一处理。
- 意义：骨架变小，横切关注点不再污染业务流程，
  这和 fnrpc 的"零擦除中间件"是同一套思路。

#### 二、"客观性"相关功能目前是写死的参数

- 现状：这些功能是作为**参数**写死在调用处的。
- 用户判断：这些**应该被抽象成插件**。
- 意义：从"传参控制行为"到"插件化扩展"，
  是架构上的一次升级；但**目前只是判断，还没做完**。
- 简历写法要区分"已实现"和"正在做"，
  不能把计划写成已完成。

#### 三、DAG 在 Rust 侧有了更多实现

- 这和第 10 条记录的状态不一样了：
  第 10 条时 Rust 侧只有 `DagShape::reseed_plan`
  （算失效名单），并行靠 TS 的 wf-demo 验证。
- 现在 Rust 侧补了更多 DAG 相关实现，具体补了哪些、
  是否已经能按声明并行，需要再看一次代码才能定稿。

#### 下一步

- 等这一轮迭代告一段落，重新看一遍
  `lib_ls/aa-workflow`：
  - 中间件层的接口长什么样
  - 插件化是否已落地、还是只有设计
  - DAG 侧新增了哪些能力、是否支持按声明自动并行
- 看完之后再决定第 10 条里的 A / B / C 三种写法。

### 14. 术语："钩子"属于插件系统，不属于中间件（已澄清）

#### 澄清

- **中间件**：只有两件事——在处理器前后插入逻辑、修改 ctx。
  所有策略（DAG、重跑复用、临时目标）都是靠这两件事自己搭的。
- **钩子**：属于**插件系统**的概念，但**底层能力仍然来自中间件**
  ——钩子只是在中间件的两件事之上，给出一套更方便的
  定义方式，不用每处都手写一遍"包 ctx.step""不调 next"。
- 所以"step 边界钩子"不是引擎原语，
  `set_step_interceptor` 只是 Rust 侧在插件系统成熟前，
  为"改写 `ctx.step`"提供的一个临时 API，即将删除。
- 简历里不应该出现"钩子"这个词，除非插件系统真落地了。

#### 已做的修正

- 引擎那行删掉了"step 边界钩子"，改为"中间件（洋葱模型）"。
- 描述里不再说"包出 step 层的洋葱"，
  改为说明"策略能做出来，是因为 `ctx.step` 是 ctx 上的
  一个函数属性"。

#### 对第 13 条的影响

- 第 13 条里写的"插件化方向"要等插件系统有实际产出后再写；
  现在只写"机制与策略分离，策略以中间件实现"，
  不写"插件"。

### 15. set_step_interceptor 已删除 → 改为 step wrapper 洋葱链

#### 代码现状（Rust `lib_ls/aa-workflow`）

- `docs/decisions/0016-step-onion.md`（2026-11-03 已接受）：
  拦截器从「单值槽位 + 判定枚举」改为**可叠加的 wrapper 链**。
- 删除清单：`set_step_interceptor`、`StepInterceptor`、
  `StepEvent`、`StepAction`。
- 新 API：`sc.push_step_wrapper(w)`，一次 step 调用是一个
  `StepCall { step_id, run_meta, next }`，wrapper 在
  `next()` 前后包逻辑，不调 `next` 即短路。
- 语义与中间件洋葱同构：**先注册者在最外层**。
- 泊车不再用 `StepAction::Park`，改为
  "不调 `next` + 把结果替换成泊车信封"。

#### 为什么要改

- 单值槽位是**后装覆盖先装**：
  `interim_goals` 和 `reseed` 同时挂载时只有后者生效，
  前者**静默消失**。TS 侧 `ctx.step = wrapped` 也是同一问题。
- 改成链之后，优先级由嵌套层级决定，可预测、不静默丢弃。

#### 一个重要的实现细节（0016 的论证）

"Rust 的 `ctx.step` 能不能像 JS 那样赋值覆盖"——
答案是能，但**不该放在 ctx 上**：

| 层 | 做法 |
| --- | --- |
| 方法 | `ctx.step::<T>()` 签名不变，泛型在方法上 |
| 属性 | 全擦除的实现挂在 **engine** 上，不在 ctx 上 |
| 写入 | 只在装配期（server 闭包里、next 之前） |

照搬 JS（属性放 ctx 上）会丢泛型 `T`，handler 要自己 downcast，
是使用者体验的净损失。

#### 迁移状态（要记住）

- `workflow_core`：`push_step_wrapper` 已就位。
- `workflow_middlewares` 里 `dag.rs` / `reseed.rs` /
  `interim_goals.rs` **还在用 `set_step_interceptor`**，
  说明这轮迁移还没做完（或者改动未提交）。

#### 对简历的影响

- 简历里不要写"拦截器""钩子"这类具体 API 名，
  改成"策略以中间件形式叠加"即可，避免随实现变化而过时。
- 第 13 条里"插件化"仍待落地。

### 16. 开源贡献：gradio-rs #11 已合并，Zed #66120 仍 review（已改）

#### 事实

- **gradio-rs #11 已合并**（2026-09-08 提交，31 分钟后 merge）
  - 内容：Gradio 6.x 把 endpoint label 从字符串改成
    `{key, _type:"translation_metadata"}` 对象，
    导致 `ApiData.label: Option<String>` 反序列化失败
  - 做法：写 `deserialize_label` 兼容 5.x / 6.x 两种形状，
    保持公开 API 不变，补 3 个回归测试，
    并用两个公开线上 app 实测
  - 仓库：JacobLinCool/gradio-rs，31 star / 4 fork，Rust
- **Zed #66120 仍 open、无 review**（10-25 提交，9.3 万 star）

#### 顾虑与判断

- 用户顾虑："gradio-rs 知名度太低，写进简历会不会没分量"
- 结论：**该写，且比悬着的 Zed PR 更该写**
  - 合并是硬事实，31 star 不影响"做了什么"的分量
  - PR 质量不低：上游 breaking change 定位 → 向后兼容 →
    回归测试 → 线上验证，完整闭环
  - 小仓库能独立负责完整修复；
    大仓库一个 PR 挂 3 周没人看，反而不好讲

#### 简历里的处理（2026-11-14 定稿）

个人优势第 8 条改成"区分状态"的双 PR 写法：

```
8. 开源贡献（区分状态）：
   - gradio-rs #11 已合并：修复 Gradio 6.x i18n label
     反序列化兼容，含回归测试与线上 app 验证
   - 向 Zed 提交 agent 流式 tool call 修复 PR
     （#66120，review 中）
```

- 原"zed 开源贡献者（agent tool 部分）"删掉了——
  PR 未合并时这个说法经不起查。
- 已合并 / review 中分开写，被追问不会露怯。
- 11/08 资面清单里那条风险提示也随之作废。

### 17. vision-lab 挂在 LocalDub、vox-lab 单列（已改，二次修订）

> 2026-11-14 二次修订：
> 1. 第一版把 vox-lab 写成"规划+起步、仅有 submodule"，
>    是抄了旧 README 的错，实际已成型（见下）。
> 2. 用户指出：**不必把两个 lab 单列成项目，
>    写进 LocalDub 里更合适**——它们本来就是围绕
>    LocalDub 的识别/语音步骤沉淀出来的。
>    已在 LocalDub「项目业绩」加一条（视觉/声音两子项）。
>
> 3. 用户再纠正：vox-lab 应**单列成项目**（三条链路从一开始
>    就同在，不是"迁入后扩展"）；LocalDub 里仍要保留各衍生项目的
>    介绍，且按可复用性排序 **vox-lab > vision-lab >
>    aa_workflow_core > fnrpc**。
>
> 结论变化：~~简历正文不加~~ → ~~并入 LocalDub~~
> → **vox-lab 单列、vision-lab 留在 LocalDub**。

#### 问题

用户问：要不要在简历里说 LocalDub 还独立出了
一个"声音 lab"和"视觉 lab"。

#### 事实（按两个仓库当前 README / ROADMAP / git log）

| | vision-lab | vox-lab |
|---|---|---|
| 路径 | `ai_ls/vision-lab` | `ai_ls/vox-lab` |
| 定位 | 单仓库聚合**三个独立目标**：视频字幕识别（核心）、GUI 自动化测试、GUI 智能操作；共享 OCR 引擎 + 抓图/注入设施 | **语音处理多后端实验与 Rust 化工作区**：ASR（whisper）、音源分离（demucs）、TTS/VC（VoxCPM / CosyVoice）三条链路，横比 Burn/GGML/ONNX Runtime / PyTorch / 云端在 CPU/Vulkan/CUDA/ROCm 上的表现 |
| 阶段 | 字幕识别已实现，有 cpp/py/rust 三实现横比基准；11-03~11-05 有字幕对齐口径与性能优化提交 | **已成型**：`packages/` 下 11 个包（voxcpm-burn / demucs-burn / voxcpm-cloud / voice-cloning / vox-core / benchmark / research / 两个 torch server / servers_py / util）、7 个子模块、独立基准、CI、Linux/Windows 预构建资产发布 |
| 与 LocalDub | 字幕 OCR 是 LocalDub 步骤之一（字幕文字光学识别） | **大量从 LocalDub 迁入**：2026-09-10 一批 `chore: migrate ... from LocalDub`（voxcpm-burn、两个 torch server、servers_py、research、demucs/CosyVoice/demucs.cpp 子模块、benchmark 结果）。ROADMAP 明写当前主线就是"移植 — 从 LocalDub/voxlab 迁入" |

#### 关键措辞（修订）

- 真正从 LocalDub **剥离成可复用库**的是 fnrpc 和 aa-workflow。
- **vox-lab 不是"独立规划的框架"，它确实来自 LocalDub**——
  只是形态是"把整个声音侧工作区迁过去 + 扩展成三条链路"，
  不是抽一个库出来。
- vision-lab README 明写"因共享底层能力而放在一起，
  彼此并非包含关系"，它和 LocalDub 是能力交集，不是父子。
- 准确说法：**LocalDub 的字幕/语音两块能力，后来各自长成了
  独立工作区（视觉 / 声音 lab）；其中声音侧是从 LocalDub
  整批迁入后再扩展的**。不要笼统说"独立出两个库"。

#### vox-lab 现状里的两个"未落地"（别写反）

README/ROADMAP 特意标注了立项设想与实际的差异：
- **没有中央调度层 / `RuntimeConfig`**：device 在编译期由
  Cargo feature 选定（cpu/wgpu/vulkan/tch/cuda/rocm），
  一个二进制绑一个 Burn backend
- **Rust 侧不起 HTTP 服务**：是"公共逻辑库 + 每个后端一个
  独立 CLI 二进制"；HTTP 只在 Python 侧
  （voxcpm_torch_server / demucs_torch_server，FastAPI + Gradio）

讲的时候按实际形态说，别照旧 README 讲"每个 Runtime
独立起服务器 + 运行时切 device"。

#### 结论：并入 LocalDub 业绩，不单列项目（已改）

- 两个 lab 是围绕 LocalDub 的识别/语音步骤沉淀的，
  写进 LocalDub「项目业绩」里最贴合实际形态，
  也省版面（个人优势不用再加）。
- 已在业绩里加一条，措辞上把三种衍生形态分开：
  - **抽出的库**：fnrpc / aa_workflow_core（原有）
  - **实验工作区**：vision-lab / vox-lab（新增）
- 技术面上仍很值得讲：说明围绕一个产品做了系统性
  技术拆解，且 Rust 化 / 多后端推理正是主线。
- 但**别单列成项目**：两个 lab 都是多方向工作区，
  单列会显得主线发散，且要额外交代和主产品的关系。

#### 技术面怎么讲（口径）

> LocalDub 里字幕识别和语音这两块，后来各自长成了独立
> 工作区：视觉侧（vision-lab）把字幕 OCR 做成 cpp/py/rust
> 三实现的横比基准，并延展到 GUI 自动化；声音侧（vox-lab）
> 从 LocalDub 迁入后又扩展成 ASR、音源分离、TTS 三条链路，
> 在 Burn / PyTorch / 云端、CPU/Vulkan/CUDA 上做多后端
> 推理对比，还有独立基准和预构建资产。它们是和主产品
> 并行推进的实验线，不是从产品里抽出的两个库。

#### 一个能互相印证的细节

- vision-lab 的字幕 OCR 有 `rapidocr_ort`（Rust）、
  `subtitle_ocr_cpp`（ONNX Runtime 直连）、`subtitle_ocr_py`
  三实现 + `tests/bench/subtitle_ocr` 横比基准。
- 这解释了个人优势里的"熟悉 burn / ort"，以及
  "Rust 复刻 C++ 关键帧识别"不是凭空练手——
  **有一套基准环境在支撑**。
- 技术面被问"怎么验证 Rust 版和 C++ 版"，这就是答案。
- 对齐口径（11-05 文档）：15/19 段相同、4 段差 1ms，
  帧级差异仅 C++ 段尾固有的 1 帧（et 取下一句首帧 PTS-1）。
  简历里写的"段边界对齐（差 ≤1ms）"与此一致。

### 18. 字幕 OCR 迁移业绩的实测证据（已改，数字定稿）

> ⚠️ 一句话前提：这些倍率**不是语言优势**。三个实现跑的是
> 同一个 ORT 1.28.0 的同一份 C++ 内核；差的是每帧宿主侧开销
> 与进程/session 模型。详见下方「归因」与 §4.6/§4.7 拆解。

#### 来源

另一个 agent 完成实测，主文档：
`ai_ls/vision-lab/docs/ocr/ppocr/benchmark-rust-vs-py-single-frame.md`
（2026-11-14/15，同一次全量会话三实现重测，ORT 版本已落位）。

同权重、同 4 线程、同帧（40 硬链接 ×3 轮）、单帧处理耗时口径
（进程内逐帧计时，**不含**进程启动 / session 加载 / 抽帧）。

#### 主表数字（取跨会话下限，不取最有利那次）

| 对比 | 数值 | 跨会话范围 |
|---|---:|---:|
| Python v4 → Rust v4 | 442 → 365ms（**1.21×**） | 1.18–1.25× |
| Python v3 → Rust v3 | 309 → 233ms（**1.33×**） | 1.33–1.48× |
| 每帧起进程 → 常驻 session | 827 → 365ms（**2.27×**） | — |
| 迁移链 v3 同 arena 开 | 293 → 254 → 233ms（累计 1.26×） | 1.26–1.33× |

#### 简历采用写法（候选 A，已写入项目业绩）

只写有硬证据的耗时，用下限 1.2× / 2.3×，并标出处。
不写内存、不写 arena 2×2、不写迁移链分解——那些是
技术面展开素材，写进正文会被追问到表格级细节。

#### 三处必须记住的"不能写"

1. **删掉 burn**：vision-lab 里 `use burn` 命中 0 次。
   burn 在 vox-lab 语音侧（voxcpm-burn / demucs-burn），
   和字幕 OCR 无关，原句把两个工作的技术栈串了。
2. **"降低内存占用"不能无条件写**：
   - 同 arena（开，速度最优格）：v4 峰值 1163→927MB（−20%）、
     v3 972→736MB（−24%），成立；
   - 但对齐到 Python 包内默认（arena 关）：v4 翻转为
     577→630MB（Rust **+9%**），v3 基本持平。
   - 内存收益主要在 Python→C++ 那一步（972→761，−22%），
     C++→Rust 只再低 3%。
3. **"多模型串行""每帧重建 session"要说准**：
   Python 常驻进程里 3 个 session 本来就只建一次；
   "每帧重建"只发生在 CLI 每帧起进程的旧调用方式。
   2.3× 主要来自进程启动 + 解释器 + import cv2 + session 重建
   （约 385ms/帧），**不是 Python 推理慢 2.3 倍**。
   收益归"常驻 session + 批处理的进程模型"，不是语言本身。

#### 归因（技术面被问时讲）

- **核心判定（§4.6/§4.7 拆解后新增）**：三个实现最终调的都是
  **同一个 ORT 1.28.0 的同一份 C++ 推理内核**，Python 进程里
  跑的也是它。所以"语言"这个变量在模型执行里几乎不存在。
  **不能写成「Rust 语言本身算得快」**。
- 76ms 每帧差距的构成（同帧、4 线程、20 次×3 轮中位）：
  | 阶段 | Python v3 | Rust v3 | 差 |
  |---|---:|---:|---:|
  | det 预处理 | 40.1 | 14.4 | −25.7 |
  | det 推理+后处理 | 219.9 | 202.0 | −17.9 |
  | cls+裁剪/resize+rec | 41.7 | 7.9 | −33.8 |
  | 合计（不含读图） | 301.0 | 225.1 | −75.9 |
  1. **det 预处理 26ms**：纯 numpy 搬运。Python 的 40.1ms 里
     `normalize()` 占 35.7ms（`(astype(f32)*scale-mean)/std`
     每步新建 28.8MB 临时数组，一帧读写约 115MB）；
     Rust 的 SIMD resize+normalize 一趟写进 CHW，14.4ms。
  2. **cls+rec 段 34ms**：不是 rec 推理慢，是"紧跟 det 之后
     第一次跑"的惩罚。Python 单框 rec 孤立跑 7.8ms、紧跟 det
     后第一次 23.9ms、第二次 15.3ms、空转 20ms 后 9.9ms；
     C++ 的 recMs 也是 20–26ms。Python/C++ 都付这笔，Rust 付得少
     （8–13ms）。机制只测到现象，未归因到代码级（更像 ORT
     线程池 spin/唤醒，因为空转反而更快），已写进局限。
  3. **det 推理本身差 ≤2%**，不是语言：Python arena 开 204.1ms
     vs Rust 202.0ms（v4 甚至反向 329.1 vs ~331）。arena 关时
     多出的 ~13ms 是 Python 包内 `enable_cpu_mem_arena=False`
     的配置差，占了表里 det 行 18ms 的大部分。
  4. **2.27× 与语言无关**：827→365 里的 385/462ms 是
     "每帧起进程 + 重建 3 个 ORT session"，属进程模型。
- **准确说法**：Rust 版去掉了每帧约 76ms 的**宿主侧开销**
  （numpy 临时数组 26 + det→rec 首帧惩罚 34 + det 推理残差 4
  + arena 配置差 13），而不是"Rust 推理比 Python 快 1.3 倍"。
- **§4.7 补的 C++ 对照**：同 ORT 1.28.0、同窗口配对纯 det Run
  C++ 185.9 vs Rust ~186.9（≤3ms 噪声），所谓"det 推理 ~9ms 差"
  是跨窗口漂移 + 口径差。1.15×（C++→Rust）= 预处理 ~8ms
  （手写 CHW 循环 vs 一趟 SIMD）+ rec 切换 ~13–18ms + 读图 ~1ms，
  **没有一项是语言**。输入 MemoryInfo / 每帧重建输入 / session
  选项三个候选解释均被受控探针排除。
  另外发现 C++ 用 ImageNet 常量、Rust 用 0.5/0.5 是正确性差异
  （本帧不影响结果，按约束未改产品实现）。
- opencv-rs 的真实价值是**数值一致性**（warpPerspective 与 cpp
  逐位一致，CER 0.54%→0.36%、missed 1→0），不是提速：
  后处理只占 1.5ms/帧（0.6%）。
- 升级 ORT 不提速（1.28 比 1.24.4 慢 5.7%，1.32 慢 2.8%），
  唯一可测收益是一次性模型加载 115→82ms（−29%）。
- **工程上最值钱的一条**：ORT 默认全局线程池在 16 逻辑核机器上
  开 16 个 intra-op 线程，放进 4 CPU 的 cpuset 就超订，
  同一二进制同一帧 366→638ms（1.74×）。必须显式钉 4 线程。
  这比换语言、换 ORT 版本都值钱。

#### 简历那条为什么不用改

候选 A 只写了两个倍率，且各自挂在正确归因上：
- 1.2× 挂在「每帧处理耗时」（宿主侧开销，非语言）
- 2.3× 挂在「进程/session 模型」（非语言）
没有把 ORT 版本、opencv-rs、burn、语言优势算进倍率，
与 §4.6/§4.7 结论一致，故正文保持原样。

#### 提交记录（另一个 agent）

- `d14313b` §4.6 分段表 + 结论、新增只读探针与 `results/phase/`
- `813b399` 计时探针代码（默认关闭，不改产品逻辑）
- `d52b4b7` §4.7 C++ 分阶段表 + 受控探针 + 原始数据
- `packages/ocr_layout/examples/split-panels.rs` 是用户的改动，未动。

#### 口径纪律（继承第 8、11 条）

- 不用"墙钟 ÷ 帧数"当单帧耗时（旧 355/590ms 来源）。
- 全量 RTF 需空载（loadavg < 1），否则负载污染。
- 同一次会话重测，别跨会话拼数字。
- 绝对值跨会话漂 ±10%，但同会话比值只漂 ±3%，故用下限。
- 只测 1 帧（稳态单帧成本），不代表全视频分布；
  全视频 CER 引用既有 README，本次未复测。

### 19. vox-lab 的贡献归属（纠正 + 业绩草稿）

> 2026-11-17 纠正：之前按 `git log` 的提交条数判断
> "推理实现 / f16 绕法 / batching 主要来自上游"是**错的**。
> fork 点（`4d0ee01`）之后 `git diff` 有 552 行新增，
> 三样都是本人做的。**判断归属要看 diff 和代码，不看提交条数。**

#### 实际做了什么（代码位置）

**1. bf16 → f16 绕法（新建 `cli/src/bin/voxcpm-burn-vulkan.rs`）**

```rust
//! 精度由环境变量 `VOXLAB_FLOAT` 选择：`f16` / `f32`（默认 `bf16`）。
//! gfx1103 没有 bf16 matmul（cubek-matmul 会明确报 not supported），
//! 所以这类卡需要换一个浮点类型；f16 与 bf16 同为 2 字节权重，容量不变。
Ok("f16") => run::<Vulkan<half::f16, i32>>(...)
_ => run::<Vulkan<half::bf16, i32>>(...)
```

**2. 定位到上游编译器后端的 bug（`src/minicpm4/mod.rs` 注释）**

```
// cubecl Vulkan/SPIR-V bf16 path (e.g. `silu(23.75) ≈ 3616`). This is not a
// silu bug per se: bf16 *elementwise multiply* is broken in that backend
// (`10*2 = 2560`, see `examples/bf16_probe.rs`)
```

比"换个精度类型"值钱得多：从跑不起来追到编译器后端缺陷，还写了
`examples/bf16_probe.rs` 复现。

**3. 确定性 seed（补齐上游 README 明确列的未解决问题）**

- `src/locdit/unified_cfm.rs`：CFM 初始噪声加 `seed: Option<u64>`，
  `StdRng::seed_from_u64` + **手写 Box–Muller**（U(0,1]→N(0,1)，
  对齐 `Distribution::Normal`），`None` 走原路径
- `src/audiovae/layers.rs`：`NoiseBlock` 的 randn 同样加 seeded 分支
- `src/audiovae/decoder.rs` / `vae.rs`：`forward_seeded` / `decode_seeded`
  透传 seed，旧 `forward` 签名不动

**4. batched prefill / 并行片段**

- `src/voxcpm2/model.rs`：新增 `prefill_lengths`（per-batch 真实长度），
  padded 输入按索引取 last hidden state，stop check 用 argmax 出每 batch 一位；
  "When `None` the path is identical to the unbatched/serial code"
- `src/voxcpm2/wrapper.rs`：相应改动（29 行）

**5. 工程封装**

- `cli/` 整套：三后端 bin（cpu/wgpu/vulkan）、`lib.rs` 148 行、
  `wrapper.ts` 92 行（TS 侧调用）、`justfile`
- `Cargo.toml` workspace 根 + `vendor/patched`（burn-cubecl / cubecl-spirv
  的 `[patch.crates-io]` 必须在根生效才能修 bf16/SPIR-V）

#### 上游的部分（要说清，别混）

- 模型结构（MiniCPM4 LM / CFM-DiT / AudioVAE）、权重加载骨架、
  基础 Vulkan backend 接入 —— 上游 `nipah~✰!` / `jv.n`
- `docs/research/voxcpm/README.md` 里的分段耗时与后端对比表是本次实测，
  可用于技术面展开

#### 简历业绩草稿（VoxCPM 这条）

```
- VoxCPM2（TTS）Rust/Burn 推理链路：
  - 定位 cubecl Vulkan/SPIR-V 后端 bf16 elementwise multiply 有误
    （silu(23.75)≈3616、10*2=2560），写 bf16_probe 复现；
    改用 f16 后端（同 2 字节、容量不变）后 Vulkan RTF 3.9–5.6，
    优于 PyTorch CPU 的 8.29
  - 补齐上游缺失的确定性生成：CFM 初始噪声与 VAE NoiseBlock
    注入可复现 seed（手写 Box–Muller 对齐 N(0,1)），
    同一文本可稳定复现波形、可做回归测试
  - 实现 batched prefill / 并行片段（per-batch 真实长度、
    padded 输入按索引取 last hidden state；无 batch 时走原串行路径）
  - 按分段耗时（base_lm 36% / CFM+VAE+encoder 53%）决定
    只把 53% 搬 GPU、LM 留 CPU，避免全栈重写
```

#### 待用户确认

- 这条是**并入 LocalDub 业绩**（作为 vox-lab 子项展开），
  还是**单列成项目**？VoxCPM 的量级比 vision-lab 大，
  单列也不算虚。
- 是否要保留"自研"这个词——模型结构不是自研，容易误解。

### 20. 工作经历段改写（已改）：联合发起人 + 过程性产出

> 2026-11 修订：本段从"全栈工程师 + 功能列表 + 从 0 到 1
> 独立负责"改为"联合发起人 + 过程性产出"。

#### 改动与理由

| 原写法 | 现写法 | 理由 |
|---|---|---|
| 全栈工程师 | 联合发起人 | 原写法是受雇身份，与该段的实际参与方式不符 |
| 从 0 到 1 独立负责数据库/API/前端/部署 | 拆成 8 条过程性产出 | "从 0 到 1""独立负责"是主导类表述，且把多件事压成一句反而看不出做了什么 |
| 功能列表 1–5（项目评论区、邀请、团队、关注、聊天） | 删掉，改为一句平台定位 + 7 条产出 | 功能列表只说明产品有什么，不说明本人做了什么 |
| 产出：agnostic-query（放在工作内容里） | 移到工作业绩，并写清动机 | 见下 |

#### 本次新查明的三块内容

1. **模块规范**（`src/modules/readme.md`）：
   单文件 300 行黄牌 / 500 行红牌、导出 5–7、内部定义 ≤10、
   拆分三判断（滚动超 3 屏 / 上下文干扰 / 单测需 mock 5 个依赖），
   以及 constants / tables / schemas / api 四类文件的命名与放置。
2. **压缩包元信息推断**（`src/modules/minecraft/inferVersionFile.ts`，838 行）：
   按 jar/zip 内元数据判定类型与版本——
   `META-INF/neoforge.mods.toml`、`META-INF/mods.toml`、
   `mcmod.info`、`fabric.mod.json`、`pack.mcmeta`；
   解析失败回退文件名匹配（semver / 两位版本 / 日期 / 快照 `23w45a` /
   loader 关键词）。
3. **发布链路**：`Dockerfile` / `Dockerfile.bun` / `docker-compose.yml`
   + `swap.sh`（原子写 upstream 配置、`nginx -t` 通过才 reload）
   + `ecosystem.config.cjs`（app/app1/app2 → 3000/3001/3002，
   `wait_ready` + `kill_timeout`）。
4. **coding agent 协作约定**：根与 `apps/web/` 各一份 `AGENTS.md`；
   `.agents/typescript.md` 明确禁止 `as` / `satisfies` /
   手写泛型参数，要求把 schema 作为类型唯一来源、泛型参数统一 `T` 前缀；
   `.agents/workflow.md` 规定每次变更后跑
   `bun run --filter web typecheck`；`.agents/skills/` 下三个
   按包加载的本地 skill（drizzle-feature-pattern / drizzle-orm /
   tanstack-db-datasource-choice），配合 `@tanstack/intent` 使用。

#### agnostic-query 的动机修正

原写"客户端查询要和服务端查询对上"。更准确的是：
**不是消除重复代码，而是两份"相似但不同表示"的语义要手工对齐**——
客户端用 TanStack DB 的 DSL 写一遍，服务端用 Drizzle 的查询条件再写一遍，
写法和类型都不同，无法靠复制粘贴复用，只能人工保持同步；
对不上时是不报错的错数据。
所以那个库做的是**渲染无关的中间表示**，在两侧双向转换。

#### 阶段状态更正（重要）

此前多处把这段写成"未上线"，**是错的**：平台已上线运行。
现表述为"已上线运行；合同签署环节未完全实现"。

这个区别影响所有围绕该段的面试口径：
- 不能再答"没上线所以不算数"
- 可以答"上线了，跑的是认证、频道与私聊、上传与版本创建这条链路；
  联合创作的合同签署没做完"

#### 在职时间定为 2025.07-2026.06（已定）

git 依据（`mc_ls/mcc`）：
- 2026-05 有 86 次提交，**最后一次是 2026-05-15**
- 2026-06-01 有 3 次提交，之后不再有
- 2026-05-16 ~ 05-31 无提交

所以：
- 写 2026.05 会漏掉 6-01 那三次
- 写 2026.06 是对的（月份覆盖到 6 月），但**不要说"做到 6 月底"**，
  最后有效提交是 **2026-06-01**
- 6-01 那三次是收尾性质（重构代码结构、更新索引时间戳、
  删废弃工作区配置），5 月中之后节奏已停

面试口径：区间说 2025.07–2026.06，被问细节时说"最后一次提交是 6 月 1 号"。

### 21. Fixheart 段的面试文案（技术点 + 30 秒口述 + 预判追问）

> 前提：平台**已上线**，不是未上线原型。
> 该段身份是联合发起人，参与方式为投资合作，不涉及雇佣关系。

#### 一、可讲的技术点（按被追问概率排序）

**1. 压缩包元信息推断**（最容易被深挖）

- 五种元数据入口：`META-INF/neoforge.mods.toml`、`META-INF/mods.toml`、
  `mcmod.info`、`fabric.mod.json`、`pack.mcmeta`
- **`\${file.jarVersion}` 占位符**：Forge 的 `mods.toml` 常写这个，
  真实版本在 jar 的 `MANIFEST.MF` 的 `Implementation-Version`，
  要回读 manifest 再替换
- **NeoForge 版本映射**：NeoForge 版本号（`21.1.x`）≠ MC 版本
  （`1.21.1`），要把 `versionRange` 做正则重写后再匹配版本列表
- **兜底**：解析失败按文件名推断，六种版本模式（语义化 / 两位 /
  单位 / `v` 前缀 / 日期 / 快照 `23w45a`）+ 九个 loader 关键词
- 资源包 vs 数据包靠 zip 里有没有 `assets/` 或 `data/` 目录区分
- 来源：[代码]

**2. coding agent 协作约定**（差异化点）

- `.agents/typescript.md` 禁止 `as` / `satisfies` / 手写泛型参数，
  要求把类型修在源头（schema、函数签名）而不是在使用点断言
- 泛型参数统一 `T` 前缀（`TArgs` / `TReturn` / `TData`）
- `.agents/workflow.md`：每次变更后跑 `bun run --filter web typecheck`
- 三个按包加载的本地 skill，配合 `@tanstack/intent` 按需加载
- 来源：[代码]

**3. 预签名直传链路**

- `get预签名`（服务端插文件表 + 签名）→ 客户端直传 OSS → `创建版本`
- 可讲点：文件表为什么在取预签名时就插入、上传失败后残留什么
- 来源：[代码]

**4. "创建 == 上传 == 发布"**

- 代码注释：因为存在审核，这三步在用户视角是同一件事
- 可讲点：审核卡在哪、拒绝后版本如何处置、项目状态由首个版本
  审核状态推导
- 来源：[代码]

**5. 蓝绿 → pm2 的演进**

- `swap.sh`：`mktemp` → `sudo mv` 原子替换 → `nginx -t` 通过才 reload
- pm2：`wait_ready` + `kill_timeout: 3000` + `listen_timeout: 10000`，
  靠就绪信号而非固定 sleep
- 可讲点：为什么换掉蓝绿——两套端口 + 一份 upstream 配置要维护，
  切换仍需脚本触发
- 来源：[代码] + [当事人原话]

**6. agnostic-query 的动机**

- 不是消除重复，是**两份相似但不同表示的语义对齐**：
  TanStack DB DSL vs Drizzle 条件，复制粘贴不可用，
  错了不报错只出脏数据
- 来源：[当事人原话] + [代码]

#### 二、30 秒口述稿

> 这段参与的是一个面向 Minecraft 资源创作与协作的平台，已经上线运行。
> 我写需求文档把实体和频道模型定下来，实现了认证、频道私聊、成员邀请、
> 上传链路，也写了模块规范和发布流程——先 Docker + nginx 蓝绿，
> 后来换成 pm2 双实例优雅更新。技术上是 Next.js 单体起步，
> 后来拆成 bun + turbo 的 monorepo。
>
> 最花心思的是压缩包元信息推断。玩家上传的是 jar 或 zip，
> 里面可能是 Forge、NeoForge、Fabric 的 mod，也可能是资源包或数据包，
> 光靠文件名不可靠——比如 Forge 的版本号常写成 `\${file.jarVersion}`
> 占位符，得回 jar 的 manifest 里读。所以做法是先按元数据文件判定，
> 失败再退回文件名匹配，两条路都覆盖。另外因为用了 coding agent 写代码，
> 我立了一套类型规范和 skill，把"类型修在源头、不许断言"变成可执行的约束。
>
> 我喜欢把不确定的输入变成确定的模型——不管是解析一个压缩包，
> 还是在两端之间建立一个中间表示。这类工作想继续做，也希望工程规范
> 成熟一些的环境。

#### 三、预判追问

**Q：一个人写为什么定这么细的规范？**
> 正因为一个人，规范是给未来的自己用的——隔两周回来加模块，
> 靠命名规则直接找到该改哪个文件，不用重新理解结构。
> 而且我用了 coding agent，规范写得越死，agent 生成的东西
> 越不容易偏离。

**Q：用 coding agent 写代码，那这些代码算你的吗？**
> 架构、规范、模块边界是我定的，agent 负责把具体实现填进去。
> 我立的规矩是禁止类型断言、schema 作唯一来源，这直接决定了
> 它能写成什么样——它不能靠 `as` 糊过去，必须把类型修在源头。
> 每一版我都做类型检查和代码 review。

**Q：平台上线了，用户有多少？**
> [待填——必须由本人提供，不推测]

**Q：为什么只做到 6 月 1 号？**
> 最后一次提交是 2026-06-01。之后这段合作就以协商方式结束了。

#### 口径红线

- 可以说"已上线运行"，**不能**说用户数、营收、DAU（无依据）
- 可以说"参与的这段时间"，**不能**用员工 / 任职 / 入职 / 离职
- coding agent 参与要如实说，**不能**暗示所有代码都亲手敲
