# Security Report

**巡检目标：** lemons101/agentic-ai  
**巡检日期：** 2026-05-23  
**最终状态：** `needs_review`

---

## 发现的问题

### 硬编码 API Key（高危）

`openclaw.json` 中曾存在疑似硬编码的火山引擎（Volcengine）API Key：

```
apiKey: "sk-985b...5555"  （已脱敏，完整值不予展示）
```

该密钥已随 Git 提交进入版本历史，任何能访问该仓库历史记录的人均可获取完整密钥。

---

## 已完成修复（工作树）

| 文件 | 修复内容 |
|------|----------|
| `openclaw.json` | `apiKey` 已替换为占位符 `<YOUR_VOLCENGINE_CODING_API_KEY>` |
| `.gitignore` | 已补充敏感文件忽略规则（`.env`、`*.key`、`*secret*` 等） |
| `README.md` | 已补充环境变量配置说明，引导用户通过 `.env` 管理密钥 |
| `openclaw-infra/configs/.env.example` | 已补充占位符变量，提供配置模板 |

以上修复仅作用于当前工作树，**尚未提交、未 push**。

---

## 残余风险

- **needs_rotation**：疑似真实密钥已进入 Git 历史（commit `2306b46` 及更早记录）。即使当前工作树已修复，历史记录中的密钥仍可被检出。**必须立即人工吊销并轮换该 Volcengine API Key。**
- **needs_review**：当前未执行 Git 历史清理（`git filter-repo` / BFG 等），历史中的明文密钥依然存在。
- 若该仓库曾被 fork 或镜像，历史清理后仍需联系相关方同步清理。

---

## 本次未执行

- 未 `git push`
- 未创建 Pull Request
- 未清理 Git 历史（需人工评估后执行）
- 未读取 `.env`、私钥、SSH Key、Cookie 或生产配置文件

---

## 建议后续操作

1. **立即吊销** `sk-985b...5555` 对应的 Volcengine API Key，并生成新密钥。
2. 将新密钥配置到部署环境的环境变量或密钥管理系统中，**不要写入任何被 Git 追踪的文件**。
3. 评估是否需要清理 Git 历史（`git filter-repo --path openclaw.json --invert-paths` 或 BFG Repo Cleaner），并与团队协商执行时机。
4. 提交并 push 当前工作树的修复变更，经 Code Review 后合并。
5. 检查 CI/CD 流水线中是否存在其他硬编码密钥。
