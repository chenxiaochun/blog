# AI 时代前端工程体系理念

## 背景

前端工程应该进入一个新的阶段。

过去的前端工程主要围绕“人”来设计：

```Plain Text
代码怎么组织？
组件怎么复用？
状态怎么管理？
接口怎么封装？
团队怎么协作？
文档怎么维护？
```

但在 AI Coding Agent 普及之后，前端工程面对的问题发生了变化。

现在的问题不只是：

```Plain Text
人怎么写代码？
```

而是：

```Plain Text
人和 AI 如何共同理解、生成、验证和演进代码？
```

传统前端项目中，大量上下文都存在于开发者脑子里：

```Plain Text
这个项目为什么这样分目录？
哪些代码是历史遗留？
哪些文件不能随便改？
为什么表单组件不能直接请求 API？
为什么接口数据不能放进全局 store？
为什么某些旧代码不能作为新代码参考？
哪些模式是团队认可的？
哪些坑以前踩过？
```

这些信息通常散落在：

```Plain Text
README
Wiki
飞书文档
代码注释
PR 讨论
老员工经验
团队口头约定
开发者脑子里
```

对人来说，这些上下文可以通过长期协作慢慢理解。

但对 AI Coding Agent 来说，这些上下文如果没有被结构化表达，就很难稳定遵守。

因此，AI 时代的前端工程需要重新定义。

---

## 核心判断

AI 时代的前端架构，不应该只围绕“代码组织”设计，而应该围绕以下四件事重新设计：

```Plain Text
上下文如何组织
规则如何表达
生成如何约束
结果如何验证
```

传统前端架构回答的是：

```Plain Text
代码应该放在哪里？
```

AI 时代前端架构要回答的是：

```Plain Text
人和 AI 如何知道什么是对的？
AI 如何知道什么不能做？
AI 写完之后怎么证明是对的？
这次开发产生的经验如何沉淀到下次？
```

所以，前端工程的核心正在从：

```Plain Text
组织代码
```

升级为：

```Plain Text
组织规格、上下文、规则、技能、验证和经验
```

---

## 新的前端工程定义

传统前端项目可以理解为：

```Plain Text
前端项目 = 代码 + 文档 + 人脑经验
```

AI 时代的前端项目应该升级为：

```Plain Text
前端项目 =
代码
+ Feature Spec
+ Engineering Rules
+ Agent Skills
+ Project Context
+ Verification
+ Decision Memory
+ Learning Loop
```

也就是说，代码不再是唯一的一等公民。

新的前端工程中，以下内容都应该成为一等公民：

```Plain Text
需求规格
UI 状态
行为流程
API 合约
权限规则
组件边界
架构决策
验证策略
AI 执行规则
历史经验
```

这些内容不应该只存在于人脑或散乱文档里，而应该变成 AI 和人都能读取、执行、校验、沉淀的工程资产。

---

## 核心理念一句话

> AI 时代的前端工程，不是让 AI 更自由地写代码，而是让 AI 在规格、规则、技能和验证体系中稳定地执行任务。
> 
> 

换句话说：

```Plain Text
AI 负责生成
人负责判断
CLI 负责约束
Schema 负责边界
Rules 负责限制
Skills 负责方法
Verify 负责质量
Learn 负责进化
```

---

## 为什么不能只靠 Prompt

很多团队使用 AI 写代码时，常见方式是：

```Plain Text
复制一堆上下文
写一段长 prompt
让 AI 生成代码
发现不对再补充
再让 AI 修改
```

这种方式短期有效，但长期不可持续。

原因是：

```Plain Text
1. Prompt 不稳定
2. 上下文容易遗漏
3. AI 不知道项目边界
4. 历史决策无法继承
5. 生成结果缺少验证
6. 经验不能沉淀
7. 每次开发都像重新开始
```

真正可靠的 AI 前端工程，不应该依赖一次性的长 prompt，而应该依赖一套持续存在的工程系统：

```Plain Text
Spec
Rules
Skills
Workflows
Verification
Memory
```

Prompt 只是执行入口，不应该是项目知识的唯一载体。

---

## 新的基本工程单元：Feature Workspace

传统前端项目中，一个功能经常分散在多个目录：

```Plain Text
pages/user.tsx
components/UserForm.tsx
hooks/useUser.ts
services/user.ts
types/user.ts
```

对人来说可以理解，但对 AI 来说，功能边界并不清晰。

AI 时代更适合把一个业务功能视为完整的工程单元，即：

```Plain Text
Feature Workspace
```

一个功能不应该只有代码目录，还应该有对应的规格目录。

示例：

```Plain Text
specs/features/user-management/
├─ spec.json
├─ feature.spec.md
├─ ui.spec.md
├─ data.spec.md
├─ behavior.spec.md
├─ verify.spec.md
├─ plan.md
├─ tasks.md
├─ verify-report.md
└─ learn-report.md
```

对应代码目录：

```Plain Text
src/features/user-management/
├─ api.ts
├─ queries.ts
├─ schema.ts
├─ components/
├─ hooks/
└─ tests/
```

关系是：

```Plain Text
specs/features/user-management/
        ↓
src/features/user-management/
```

这意味着：

```Plain Text
规格定义目标
计划定义路径
任务定义执行
代码完成实现
验证证明正确
经验反哺系统
```

---

## Spec 的角色

Spec 不应该只是需求文档。

在 AI 时代，Spec 应该是：

```Plain Text
人和 AI 共同使用的工程合约
```

一个好的前端 Feature Spec 应该回答：

```Plain Text
这个功能是什么？
它解决什么问题？
它属于哪个业务域？
它对应哪个路由？
本次范围是什么？
不做什么？
有哪些 UI 状态？
有哪些用户行为？
涉及哪些 API？
涉及哪些权限？
失败时怎么处理？
组件如何拆分？
哪些组件不能请求 API？
怎么验证它是对的？
```

Spec 的目标不是写得漂亮，而是：

```Plain Text
结构清晰
边界明确
可执行
可验证
可沉淀
```

---

## Spec 不应该让用户手写

让用户从空白 Markdown 开始写 spec，是不现实的。

大多数用户没有耐心写长文档，也没有耐心读 AI 自动生成的一堆文档。

更合理的方式是：

```Plain Text
用户用自然语言描述需求
  ↓
AI 理解需求
  ↓
AI 智能追问关键问题
  ↓
用户回答和确认
  ↓
Agent 生成结构化 spec JSON
  ↓
CLI 校验和渲染
```

也就是说：

> Spec 不是用户写出来的，而是通过 AI 访谈“采访”出来的。
> 
> 

用户只需要做三件事：

```Plain Text
表达需求
回答关键问题
确认摘要
```

系统负责把这些信息转化为结构化规格。

---

## AI 访谈的价值

AI 在 spec 创建阶段的角色，不是“文档生成器”，而是：

```Plain Text
需求访谈官
产品经理
前端架构师
风险提醒者
```

它应该根据用户的需求智能判断：

```Plain Text
这是 CRUD 页面？
这是详情页？
这是表单流程？
这是设置页？
这是多步骤工作流？
这是数据看板？
```

然后追问真正有价值的问题。

例如用户说：

```Plain Text
做一个用户管理页面，可以搜索、分页、新增和编辑用户
```

AI 应该追问：

```Plain Text
1. 页面路由是否使用 /admin/users？
2. 新增和编辑是弹窗表单、抽屉表单，还是独立页面？
3. 提交失败时是否保留用户输入？
4. 是否需要权限控制？
5. 删除、批量导入、角色分配是否不在本次范围？
```

这些问题能显著降低后续实现的不确定性。

---

## 用户交互应该是低摩擦的

Spec 创建不应该是：

```Plain Text
用户填写复杂表单
用户阅读长文档
用户维护大量上下文
```

而应该是：

```Plain Text
AI 提供建议
用户选择或确认
系统生成结构化结果
```

交互形式应优先使用：

```Plain Text
选择题
多选题
确认题
短答案
```

减少开放式长回答。

例如：

```Plain Text
新增和编辑使用哪种交互形式？

❯ 弹窗表单
  抽屉表单
  独立页面
```

而不是：

```Plain Text
请详细描述新增和编辑用户的完整交互流程。
```

---

## 为什么需要结构化 JSON

AI 可以生成文档，但不能直接把 AI 生成的 Markdown 当作系统事实来源。

因为 Markdown 容易：

```Plain Text
结构不稳定
字段缺失
标题变化
难以校验
难以被 CLI 解析
难以驱动后续自动化
```

所以更合理的方式是：

```Plain Text
Agent 生成 JSON
CLI 校验 JSON
CLI 渲染 Markdown
```

也就是说：

```Plain Text
spec.json 是机器事实来源
Markdown 是人类阅读视图
```

流程：

```Plain Text
Agent 生成 FeatureSpec JSON
  ↓
CLI 用 Schema 校验
  ↓
CLI 展示中文摘要
  ↓
用户确认
  ↓
CLI 写入 spec.json
  ↓
CLI 渲染 feature.spec.md / ui.spec.md / data.spec.md / behavior.spec.md / verify.spec.md
```

这样可以保证：

```Plain Text
可读
可控
可校验
可复用
可演进
```

---

## Agent\-first，而不是 API\-first

`ai-fe` 不应该默认要求用户配置 `OPENAI_API_KEY`。

原因是，用户往往已经在使用：

```Plain Text
Cursor
Claude Code
Codex
ChatGPT Agent
Gemini CLI
其他 AI Coding Agent
```

这些 Agent 已经具备模型能力、代码读取能力、文件修改能力、命令执行能力。

因此，`ai-fe` 更适合成为：

```Plain Text
当前 AI Agent 的前端工程工具层
```

而不是自己成为一个模型调用工具。

正确分工是：

```Plain Text
当前 Agent：
- 理解需求
- 追问用户
- 生成 spec JSON
- 生成 plan JSON
- 实现代码
- 总结经验

ai-fe CLI：
- 提供 schema
- 生成 Agent 任务
- 校验 JSON
- 渲染 Markdown
- 管理 feature workspace
- 聚合 prompt
- 执行 verify
- 生成 report
```

一句话：

> Agent 负责智能，CLI 负责确定性。
> 
> 

---

## 为什么需要 CLI

CLI 是最适合第一阶段落地的产品形态。

原因：

```Plain Text
1. 前端开发者天然熟悉 CLI
2. CLI 可以直接进入现有项目
3. CLI 可以和任意 AI Coding Agent 共存
4. CLI 可以管理文件协议
5. CLI 可以执行验证命令
6. CLI 可以被未来 MCP / Skills 调用
```

它不是传统脚手架：

```Plain Text
生成项目模板
```

而是：

```Plain Text
给前端项目安装 AI 工程层
```

CLI 的职责不是写代码，而是建立完整闭环：

```Plain Text
create
apply
plan
apply-plan
prompt
verify
learn
```

---

## 新项目和老项目如何落地

### 14\.1 新项目

新项目可以直接采用 AI\-native 工程结构。

要求：

```Plain Text
所有新功能必须先有 spec
所有功能必须有 plan / tasks
所有开发必须有 verify
重要决策必须写 ADR
重复经验必须沉淀为 rule / skill
```

新项目适合从一开始就建立：

```Plain Text
specs/
.ai/
verification/
src/features/
```

---

### 14\.2 老项目

老项目不应该一上来重构。

正确策略是：

```Plain Text
不动历史代码
先外挂 AI 工程层
新功能走新流程
触碰到的旧代码逐步治理
关键模块优先补验证
```

也就是：

```Plain Text
新项目用它定义标准
老项目用它包围历史
```

老项目第一步不是改代码，而是增加：

```Plain Text
.ai/
specs/
verification/
```

然后通过：

```Plain Text
ai-fe init
ai-fe scan
```

建立项目画像和 AI 规则。

---

## 项目画像 Project Profile

AI 要稳定工作，首先需要理解当前项目。

`ai-fe scan` 应该生成：

```Plain Text
.ai/project-profile.md
```

它记录：

```Plain Text
技术栈
包管理器
构建命令
测试命令
目录结构
路由方案
状态管理
表单方案
API 请求方式
UI 组件库
高风险文件
AI 修改限制
```

项目画像的价值是：

```Plain Text
给 Agent 一张项目地图
```

让 Agent 不需要每次都重新猜：

```Plain Text
这个项目用什么框架？
接口怎么封装？
测试命令是什么？
哪些文件不能乱改？
```

---

## Rules 的角色

Rules 是硬性工程规则。

它回答：

```Plain Text
AI 不能做什么？
代码必须遵守什么？
哪些边界不能突破？
```

示例：

```Plain Text
展示组件不能直接请求 API
表单提交失败不能清空用户输入
features 之间不能随意互相 import
AI 不能顺手重构无关文件
AI 不能修改 protected files
```

Rules 的作用不是解释为什么，而是约束行为。

好的 Rule 应该具体、可执行、可检查。

不好的 Rule：

```Plain Text
代码应该优雅。
组件应该清晰。
```

好的 Rule：

```Plain Text
UserTable 不能 import API client。
UserFormModal 提交失败时不能主动关闭弹窗。
shared/components 不允许依赖 features/*。
```

---

## Skills 的角色

Skills 是可复用的开发能力。

它回答：

```Plain Text
AI 遇到某类任务时，应该怎么做？
```

例如：

```Plain Text
build-page
build-form
integrate-api
write-test
refactor-safely
```

一个 Skill 应该包含：

```Plain Text
什么时候使用
必须遵守什么
应该输出什么
如何验证
常见反模式
```

示例：

```Plain Text
build-form Skill：
- 表单校验要独立
- 提交失败要保留输入
- API 错误要展示在表单层
- 表单组件不能直接请求 API
- 必须覆盖成功和失败测试
```

Rules 是约束，Skills 是方法。

---

## ADR 的角色

ADR 是 Architecture Decision Record，即架构决策记录。

它回答：

```Plain Text
我们为什么这么决定？
当时有哪些备选方案？
这个决策带来什么后果？
以后 AI 和开发者应该遵守什么？
```

ADR 不记录普通小改动，只记录会影响长期工程结构的决策。

适合写 ADR 的内容：

```Plain Text
是否采用 feature-based 结构
服务端状态是否使用 React Query
表单组件是否保持 presentational
API client 的边界
AI 修改代码的安全边界
权限逻辑放在哪里
```

ADR、Rule、Skill 的关系：

```Plain Text
ADR 解释原因
Rule 形成约束
Skill 指导执行
```

例如：

```Plain Text
ADR：
我们决定表单组件不直接请求 API。

Rule：
表单组件不能 import API client。

Skill：
构建表单时，onSubmit 必须由外部传入，mutation 放在 feature container 或 query hook 中。
```

---

## Verification 的核心地位

AI 时代，代码生成会越来越容易。

真正稀缺的是：

```Plain Text
怎么证明生成的代码是对的？
```

所以 Verify 必须成为前端工程核心能力。

验证不应该只靠人肉 Review，而应该包括：

```Plain Text
typecheck
lint
build
unit test
component test
architecture check
acceptance criteria check
protected files check
```

AI 写完代码后，系统应该先验证：

```Plain Text
能不能编译？
有没有类型错误？
有没有 lint 错误？
有没有测试失败？
有没有违反架构规则？
有没有漏掉验收标准？
有没有改到不该改的文件？
```

目标是：

```Plain Text
AI 写完，系统先打分
人再做最终判断
```

---

## Learn 的意义

传统开发是：

```Plain Text
开发一次，代码变多
```

AI\-native 工程应该是：

```Plain Text
开发一次，系统变聪明一次
```

每次开发后，都应该问：

```Plain Text
AI 这次犯了什么错？
这次人工 Review 提了什么意见？
哪些问题可以沉淀为 Rule？
哪些模式可以沉淀为 Skill？
哪些决策应该写 ADR？
哪些测试 helper 可以复用？
```

这就是 Learn 的意义。

Learn 的输出可以包括：

```Plain Text
learn-report.md
新增 rule 建议
更新 skill 建议
新增 ADR 建议
新增测试 helper 建议
新增 protected file 建议
```

长期看，Learn 会让项目越来越适合 AI 参与开发。

---

## 为什么要区分死文档和活规格

传统文档容易变成死文档：

```Plain Text
写完没人看
看了不一定信
代码变了文档没变
AI 读了也不知道是否可信
```

AI\-native 工程中的文档应该分成三类：

### 21\.1 Source of Truth

需要人认真确认：

```Plain Text
产品目标
业务规则
权限规则
关键体验原则
架构决策
```

### 21\.2 Generated Context

由系统自动生成：

```Plain Text
项目画像
路由结构
组件依赖
API 使用关系
验证报告
```

### 21\.3 Agent Working Context

每次任务动态生成：

```Plain Text
当前功能规格
相关规则
相关技能
相关文件
禁止修改的边界
验证命令
```

核心原则：

```Plain Text
不要维护永远过期的大文档
而要生成当前任务需要的活上下文
```

---

## 文件协议的重要性

`ai-fe` 的核心不是某个命令，而是一套文件协议。

这些文件构成项目的 AI 工程资产：

```Plain Text
.ai/config.json
.ai/state.json
.ai/schemas/*
.ai/tasks/*
.ai/rules/*
.ai/skills/*
.ai/workflows/*
.ai/memory/*
specs/features/*/spec.json
specs/features/*/*.spec.md
specs/features/*/plan.md
specs/features/*/tasks.md
specs/features/*/verify-report.md
specs/features/*/learn-report.md
verification/config.json
```

CLI 围绕这些文件执行：

```Plain Text
生成
校验
渲染
聚合
验证
沉淀
```

这样做的好处：

```Plain Text
开放
轻量
可版本管理
可被 Agent 读取
可被团队 Review
可接入 CI
可扩展到 MCP
```

---

## 为什么 Prompt 仍然重要

虽然不能只靠 Prompt，但 Prompt 仍然重要。

区别在于：

```Plain Text
过去的 Prompt：
一次性、手写、上下文不完整、不可复用

ai-fe 生成的 Prompt：
由 specs / rules / skills / plan / tasks 聚合生成
结构稳定
上下文完整
可复现
可审查
```

`ai-fe prompt` 的作用是：

```Plain Text
把当前 feature 的所有工程上下文打包给 AI Coding Agent
```

内容包括：

```Plain Text
功能规格
UI 规格
数据规格
行为规格
验证规格
实施方案
任务拆解
项目画像
工程规则
相关 Skills
硬性限制
完成后汇报要求
```

这样，Agent 不再靠猜，而是在明确上下文中执行。

---

## 最终闭环

完整 AI\-native 前端工程闭环是：

```Plain Text
自然语言需求
  ↓
ai-fe create 生成 Agent 任务
  ↓
Agent 追问用户
  ↓
Agent 生成 spec JSON
  ↓
ai-fe apply 校验和落盘
  ↓
ai-fe plan 生成计划任务
  ↓
Agent 生成 plan JSON
  ↓
ai-fe apply-plan 校验和落盘
  ↓
ai-fe prompt 生成开发上下文
  ↓
Agent 实现代码
  ↓
ai-fe verify 验证结果
  ↓
ai-fe learn 沉淀经验
```

这个闭环的价值不只是“生成代码”，而是：

```Plain Text
让每次开发都有规格
让每次实现有计划
让每次生成有约束
让每次交付有验证
让每次经验能沉淀
```

---

## 产品的本质

`ai-fe` 表面上是一个 CLI。

但本质上，它是：

```Plain Text
一套面向 AI Coding Agent 的前端工程协议
```

它把前端开发从：

```Plain Text
需求 → 人理解 → 人写代码 → 人检查
```

升级为：

```Plain Text
需求 → AI 访谈 → 结构化规格 → 计划 → Agent 实现 → 系统验证 → 经验沉淀
```

---

## 与传统脚手架的区别

传统脚手架：

```Plain Text
生成项目模板
生成目录
生成初始代码
```

`ai-fe`：

```Plain Text
安装 AI 工程层
生成 Agent 任务
提供 Schema
管理 Spec
管理 Rules
管理 Skills
生成 Prompt
执行 Verify
沉淀 Learn
```

传统脚手架解决的是：

```Plain Text
如何开始一个项目
```

`ai-fe` 解决的是：

```Plain Text
AI 如何长期、稳定、可控地参与前端项目开发
```

---

## 与 AI IDE 的关系

`ai-fe` 不和 AI IDE 竞争。

它应该和这些工具协作：

```Plain Text
Cursor
Claude Code
Codex
ChatGPT Agent
Gemini CLI
VS Code Agent
```

这些工具负责：

```Plain Text
智能生成
代码修改
局部重构
测试补齐
```

`ai-fe` 负责：

```Plain Text
工程约束
任务协议
规格管理
上下文组织
验证报告
经验沉淀
```

所以它不是 AI IDE，而是：

```Plain Text
AI IDE 前面的工程控制层
```

---

## 长期愿景

短期，`ai-fe` 是一个 CLI。

中期，`ai-fe` 是一套前端 AI 工作流系统。

长期，`ai-fe` 可以成为：

```Plain Text
企业前端工程知识系统
```

它可以帮助团队回答：

```Plain Text
我的团队规范能不能被 AI 遵守？
我的历史决策能不能被 AI 继承？
我的设计系统能不能被 AI 正确使用？
我的接口规范能不能被 AI 自动接入？
我的质量标准能不能被 AI 自动验证？
我的经验能不能被 AI 下次复用？
```

最终目标不是让 AI 写更多代码，而是让整个前端工程体系具备：

```Plain Text
可理解
可执行
可验证
可沉淀
可进化
```

---

## 最重要的原则

### 29\.1 不要让用户写 Spec

用户只需要表达需求、回答问题、确认摘要。

### 29\.2 不要让 AI 自由写代码

AI 必须在规格、规则、计划和验证体系中执行任务。

### 29\.3 不要让 CLI 绑定模型厂商

AI 能力来自当前 Agent，CLI 负责确定性工程能力。

### 29\.4 不要把文档当成最终目的

文档只是人类阅读视图，结构化 JSON 才是机器事实来源。

### 29\.5 不要一开始重构老项目

老项目先外挂 AI 工程层，新功能走新流程，历史代码逐步治理。

### 29\.6 不要只生成代码

每次开发都要沉淀规则、技能、决策和经验。

---

## 一句话总结

> AI 时代的前端工程，不是从“人写代码”变成“AI 写代码”，而是从“靠人脑维护上下文”变成“靠规格、规则、技能、验证和经验沉淀驱动人机协作”。
> 
> 

`ai-fe` 要做的，就是把这个理念落地成一套可以安装到任意前端项目中的工程系统。

