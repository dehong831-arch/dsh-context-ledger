# 贡献指南

欢迎提交可复现的问题和小范围改进。请先阅读 [项目说明](README.md) 与 [验证记录](docs/VALIDATION.md)，了解当前兼容目标及测试边界。

## 准备环境

使用 **Node.js 24** 和 npm 开发。插件运行时下限是 Node.js 22.16.0，开发和 CI 统一使用 Node.js 24。DSH 开发依赖固定为 **`0.2.1-rc.5`**，请保留 [依赖锁文件](package-lock.json)，在本仓库根目录安装：

```powershell
npm ci
```

宿主相关测试优先读取显式环境变量 `DSH_TEST_ROOT`；未设置时自动解析本地 `@deepseek-ai/dsh` 开发依赖。因此常规贡献不需要全局安装 DSH，也不需要使用个人 profile。若显式设置该变量，目标必须是精确版本 `0.2.1-rc.5` 的 DSH 包根目录。

## 本地验证

在本仓库根目录运行：

```powershell
npm test
npm run check
npm run audit:public
npm pack
node scripts/verify-package.mjs ./dsh-context-ledger-0.1.1.tgz
```

- `npm test` 验证归档、检索、官方压缩服务、原生工具集成、preset 生成和真实 Agent 协议；使用自建 fixture 和离线确定性模型适配器。
- `npm run check` 检查 ESM 语法及导出目标。
- `npm pack` 生成实际分发包；检查包内仅含公开源码、必要脚本、示例、许可证和文档。
- [包验证脚本](scripts/verify-package.mjs) 在隔离临时目录解包，通过公开 Cordis Loader 加载真实安装包；不使用测试 import hook。执行环境应能从 `PATH` 调用 `tar`。

若改变版本号，请同步更新安装包命令中的文件名，并核对 [版本记录](CHANGELOG.md)。CI 的具体检查见 [工作流目录](.github/workflows/)；新增或修改配置后，请以实际 GitHub Actions 结果为准。

## 变更原则

- 继续通过宿主公开持久化接口及官方 `BasicCompactionEngine` 扩展点工作。摘要前必须完成当前源日志前缀归档，失败时不得替换历史。
- 保持当前会话身份约束、fork 子引用、不可变引用与游标快照语义。模型工具不能接受任意会话 ID 作为访问入口。
- 调整归档格式、文本提取或分页逻辑时，同时考虑已有数据库、重放一致性、重启和 Unicode 码点边界。
- 调整额度时明确单位为 JSON UTF-8 字节；并发 native/PTC 工具调用必须共享当前会话的每轮额度。
- 修改 preset 生成器时保留完整声明和不透明 `!!js` 节点，不执行用户配置表达式。

修复行为问题时提供最小回归用例。单纯的文档或格式改动只需检查相应内容、链接及打包结果，无需添加重复实现细节的测试。

## 问题报告和 Pull Request

报告问题时提供插件、Node.js、DSH 版本、操作系统、最小复现步骤、预期行为和实际结果。使用合成会话与脱敏配置复现；不要上传真实会话原文、数据库、有效配置导出、诊断包或凭据。敏感漏洞请遵循 [安全说明](SECURITY.md)。

Pull Request 说明应描述具体触发条件、最终行为及已完成的验证。与真实供应商模型相关的结论需有独立证据，不能从离线测试推导摘要质量、费用或长期目标保真率。

提交前先确认 Git 工作目录就是这份独立的公开仓库，并核对差异和暂存区。只暂存本次修改的具体文件，不把父目录或个人运行目录纳入提交范围。运行数据、trial、deployment 和 backups 应留在仓库外。

项目采用 [MIT License](LICENSE)，贡献请遵循现有许可证及 contributors 署名，不推测或增补个人身份信息。
