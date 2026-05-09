---
name: agent-product-layout
description: |
  agent 产品文件夹规范 · 4 仓嵌套 + 6 域名规约 + members/ 递归 · 大仓 markdown 真源含 4 实体 + 公共 lib · 不在 1 个仓铺平。
  TRIGGER when 老板/lead 起新 agent 产品 / 改 agent 文件结构 / 老板问 "agent 文件夹怎么布局 / xiaoxi 范式 / members 嵌套 / 主 agent 在哪 / 4 仓哪 4 个 / 怎么拆"。
  DO NOT TRIGGER when 是单仓代码改动 (不涉及结构) · 是已熟范式只问 1 个具体路径 · 是 GHA / 部署 (用 agent-env-split + fc-agent-deploy + aliyun-static-site)。
argument-hint: ""
category: meta
allowed-tools: Bash, Read, Edit, Write
---

# agent-product-layout · agent 产品文件夹规范

## 一句话

老板 5-9 拍 · 1 个 agent 产品 = 1 个主 agent + 4 GitHub 私仓 + members/ 递归 · 不铺开。

参考 xiaoxi 范式 · 不脑补别的结构。

## 老板原话

> "agent 的文件夹的管理也是一个 skill" (5-9)

> "我们的 agent 文件规范 · 并不是铺开的 · 而是参考 xiaoxi" (5-9)

> "product = 主 agent · 1:1 · 主 agent 在产品大仓顶层" (5-9 推断)

## 4 仓硬约束 (1 agent 产品 = 4 GitHub 私仓)

```
yarnovo/<product>            产品大仓 · markdown 真源 · 含 4 实体 + members/ 递归 · 没代码
yarnovo/<product>-agent      LLM runtime · Node 22 + pi-ai + FC v3 Custom Container
yarnovo/<product>-api        业务 API · FastAPI + uv + RDS/sqlite + FC v3
yarnovo/<product>-app        用户 H5 · Vite + React + OSS 静态站
yarnovo/<product>-admin      (可选) 运维后台 · OSS 静态站 · xiaoxi 已有
```

每仓独立 build / deploy / GHA workflow / vault.json / maintainer。

## 递归结构 (product = 主 agent · 1:1)

```
~/.claude/repos/<product>/                       产品大仓 = 主 agent (顶层 · 不嵌套 members/<product>/)
├── README.md                                    产品总览 (老板 phone 入口)
├── agent.yaml                                   主 agent 5 段 prompt (顶层)
├── agent/                                       submodule yarnovo/<product>-agent (LLM runtime 后端)
├── api/                                         submodule yarnovo/<product>-api
├── app/                                         submodule yarnovo/<product>-app
├── admin/                                       submodule yarnovo/<product>-admin (可选)
├── interviews/                                  主 agent 4 实体
│   └── I-NNN-<slug>.md
├── intros/
│   └── INTRO-NNN-<slug>.md
├── scenarios/
│   └── S-NNN-<slug>.md
├── gaps/
│   └── GAP-NNN-<slug>.md
├── issues/
│   ├── requirements/
│   │   └── REQ-NNN-<slug>.md
│   └── defects/
│       └── DEFECT-NNN-<slug>.md
└── members/                                     sub agents (递归 · 默认空 · 多 agent 时加)
    └── <sub_agent>/                             sub agent (跟外面 <product>/ 同形态)
        ├── README.md
        ├── agent.yaml                           sub agent prompt
        ├── agent/                               (mvp 复用主 agent · README 注明)
        ├── app/                                 submodule yarnovo/<product>-<sub>-app
        ├── interviews/  intros/  scenarios/  gaps/  issues/
        └── members/                             sub-sub agents (默认空)
```

**关键**: 主 agent 直接是产品顶层 (`xiaoxi/agent.yaml` = 红娘小喜) · `members/` 只放 sub agents (`xiaoxi/members/admin/` = 老板助理)。

**包含但不限于** 4 实体 + issues · 后续每个 agent 可加 `wiki/` / `assets/` / `templates/` 等专属目录。

## 6 域名规约表 (跟 agent-env-split 联动)

每个 agent 6 域名 = prod 3 + staging 3:

| 段 | prod | staging | 部署仓 |
|---|---|---|---|
| m | `m.<product>.agentaily.com` | `staging.m.<product>.agentaily.com` | `<product>-app` |
| api | `api.<product>.agentaily.com` | `staging.api.<product>.agentaily.com` | `<product>-api` |
| agent | `agent.<product>.agentaily.com` | `staging.agent.<product>.agentaily.com` | `<product>-agent` |

可选第 4 段 admin: `admin.<product>.agentaily.com` (OSS · `<product>-admin`) · 加 6 → 8 域名。

**域名实施 / GHA workflow 不在本 skill 范围** · 看 `agent-env-split` skill。

## 公共 lib (跨产品共享)

```
~/.claude/repos/lib/                              跨产品共享 lib (各仓独立 + git submodule)
├── auth/                                          (例) 手机号登录 + JWT shared
├── pi-runtime/                                    pi-ai agent runtime wrapper
├── llm-tools/                                     通用 LLM tool 包
├── ui/                                            @aily-ui 组件库 (历史在 repos/ui)
└── akong-intro-astro/                             intro 站 Astro 模板
```

YAGNI · 当前各 lib 独立 · 真有跨产品重复时再抽。

## 4 实体 markdown (phone-only 友好)

| 实体 | 路径 | 文件名 | frontmatter | 用途 |
|---|---|---|---|---|
| INTERVIEW | `<product>/interviews/` | `I-NNN-<slug>.md` | `id`, `interviewee`, `role`, `date` | 用户采访真声音 |
| INTRO | `<product>/intros/` | `INTRO-NNN-<slug>.md` | `id`, `parent_interview` | 自我介绍 / 价值主张 |
| SCENARIO | `<product>/scenarios/` | `S-NNN-<slug>.md` | `id`, `parent_intro`, `priority` | 端到端测试场景 + UAT |
| GAP | `<product>/gaps/` | `GAP-NNN-<slug>.md` | `id`, `parent_scenario`, `status` | 现状-目标差距 → 子仓 issues |

仓内 issues (REQ + DEFECT) 在子仓 `<product>-<role>/issues/{requirements,defects}/` · 顶层产品大仓也可放产品级 REQ (跨子仓需求 · `parent_req` 指上层)。

真源 git markdown · **不上 db** · 老板 GitHub phone 直接看。

## Why · 为什么不铺开

- **运行时拆分** · agent runtime / API / H5 各自独立部署 · 互不阻塞 (改 agent prompt 不重 build api · 反之)
- **责任拆分** · 1 仓 1 maintainer (MVP lead 兼任) · 后续 hire 直接定向接手单仓
- **大仓 markdown 是老板视角** · git markdown 真源 · 老板 phone 直接 GitHub 看 / 不需进 admin / 不需 db
- **递归 members/** · 同 1 个产品下 N 个 agent 各自 own 自己的 4 实体 + issues · 不串

## 何时起新 agent 产品

老板说"起一个新 agent 产品 X" / "复制 xiaoxi 范式做 X":

1. 派 `agent-fullstack-bootstrapper` advisor (一次性 subagent) 走 4 仓骨架 + 6 域名部署
2. lead 自己**不动手**起 4 仓 (advisor 跑过 xiaoxi · 熟)
3. 跟 `agent-env-split` skill 联动 · 双 GHA workflow 一起建

## 现役 agent 产品

- `yarnovo/xiaoxi` (红娘小喜 + 老板助理 admin · members/{xiaoxi,admin}/)
- `yarnovo/interviewer` (阿空小问 1 个 agent · members/interviewer/)

后续阿空团队成员 (法律 / 会计 等) · 按这套规范起。

## 反例 (lead 别犯)

- ❌ 把 agent.yaml + 业务代码 + H5 全放 1 仓 (铺开 · 反规范)
- ❌ 把 interviewer 塞进 xiaoxi/members/interviewer/ (5-9 老板抓 · 应独立产品 · 1 product 1 主 agent)
- ❌ agent 是 yaml 文件不是目录 (5-9 老板抓 · 应递归子目录 · `agent.yaml` 在子目录里)
- ❌ 没大仓 markdown 真源 · 4 实体只在 db (5-9 老板抓 · 真源在 git markdown)
- ❌ 域名只 1 段 / 不区分 prod+staging (5-7 老板拍 · 必 6 域名)
- ❌ lead 自己手撸 4 仓骨架 (派 `agent-fullstack-bootstrapper` advisor)
- ❌ 嵌套 members/<product>/ 把主 agent 包一层 (主 agent = 顶层 · 不再嵌)
- ❌ 子仓嵌主仓 (如把 -agent 仓 git submodule -api · 应 4 仓平级 · 都挂大仓顶层)

## 起仓 SOP (lead 自查 · 派 advisor 前)

派 `agent-fullstack-bootstrapper` advisor 前 · lead 准备好:

1. `<product>` slug (英文小写 · 例 `xiaoxi` / `interviewer` / `xiaolaw`)
2. 主 agent 中文名 (例 "红娘小喜" / "阿空小问")
3. 角色定位 (1-2 句话)
4. 是否含 admin 子仓 (default 不含 · 真要老板拍)
5. 是否多 agent (default 单 agent · 后续 members/ 加)

advisor 自动:

- gh repo create yarnovo/<product>{,-agent,-api,-app}{,-admin?} --private
- git submodule add 进 ~/.claude/repos/<product>/{agent,api,app,admin?}
- 写 agent.yaml + README.md + 4 实体目录骨架
- 配双 GHA workflow (走 `agent-env-split` skill)
- 申域名 + DNS + cert (走 `akong-cert` + DNS skill)

## 跟其他 skill 边界

| skill | 范围 |
|---|---|
| **agent-product-layout** (本) | 4 仓嵌套 + 6 域名规约表 + members/ 递归 (产品文件结构) |
| **agent-env-split** | 双环境规约 + GHA 模板 (环境实施) |
| **agent-fullstack-bootstrapper** (advisor) | 一次性派 subagent 起 4 仓骨架 (实施) |
| **fc-agent-deploy** | FC v3 + ACR 实施细节 (单仓单环境) |
| **aliyun-static-site** | OSS 静态站实施细节 |
| **goals** | 4 实体 (INTERVIEW/INTRO/SCENARIO/GAP) markdown 模板 |
| **issues** | 仓内 REQ/DEFECT markdown 模板 |

## 入口

- 老板 phone: "起新 agent 产品 X / 我要做 Y agent" 等自然语言
- lead 起新仓 default 必先跑本 skill 校 4 仓嵌套 + 6 域名

## 关联 memory

- `~/.claude/memories/feedback_agent_product_file_layout.md` (5-9 拍 · xiaoxi 范式)
- `~/.claude/memories/feedback_agent_3_public_domains.md` (5-7 · 6 域名/agent)
- `~/.claude/memories/feedback_focus_xiaoxi_only_5_9.md` (5-9 大转型 · 聚焦)
- `~/.claude/memories/feedback_one_agent_one_repo.md` (一个 agent 一个 repo)
