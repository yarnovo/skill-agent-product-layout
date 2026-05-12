# skill-agent-product-layout

agent 产品文件夹规范 · 4 仓嵌套 + 6 域名规约 + members/ 递归 · 大仓 markdown 真源含 4 实体 + 公共 lib · 不在 1 个仓铺平。

## 4 仓硬约束

```
yarnovo/<product>            产品大仓 · markdown 真源 · 4 实体 + members/ · 没代码
yarnovo/<product>-agent      LLM runtime · Node + pi-ai + FC
yarnovo/<product>-api        业务 API · FastAPI + uv + FC
yarnovo/<product>-app        用户 H5 · Vite + React + OSS 静态站
yarnovo/<product>-admin      (可选) 运维后台 · OSS 静态站
```

## 递归

主 agent = 产品顶层 (xiaoxi/agent.yaml = 红娘小喜) · `members/<sub>/` 跟外面同形态。

## 6 域名规约 (跟 agent-env-split 联动)

| 段 | prod | staging |
|---|---|---|
| m | `m.<product>.agentaily.com` | `staging.m.<product>.agentaily.com` |
| api | `api.<product>.agentaily.com` | `staging.api.<product>.agentaily.com` |
| agent | `agent.<product>.agentaily.com` | `staging.agent.<product>.agentaily.com` |

实施细节看 `agent-env-split` skill。

详见 `SKILL.md`。

## 关联

- `agent-env-split` skill · 双环境 + GHA 实施
- `agent-fullstack-bootstrapper` advisor · 起 4 仓骨架
- `~/.claude/memories/feedback_agent_product_file_layout.md` · 老板 5-9 拍

## 分支策略

- `main` = 主分支 (= 部署源 if 该仓有部署 · 否则 sync ~/.claude submodule)
- 临时分支: `feat/<name>` · `fix/<name>` · base 永远 `origin/main`
- PR target = `main` · CI pass + mergeable + 无 conflict 才合
- lead merge 不 auto · subagent 不自合
- subagent 创 worktree 干活前必读本 README

详见 `~/.claude/memories/feedback_lead_main_branch_only.md`
