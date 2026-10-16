# Memoh - LazyCat App

[Memoh](https://github.com/felinics/Memoh)（开源多 Agent 平台：每个 Agent 拥有独立计算机、桌面、网络与长期记忆）的懒猫微服（LazyCat）打包。

## 模式

- **镜像模式（mirror）**：`memohai/server`、`memohai/web`、`postgres`、`pgvector` 镜像经 `docker.1ms.run` 加速器拉取，`require_digest_match: true` 校验镜像内容与上游一致。
- **自动跟随上游**：每日检查上游 semver tag（server/web 同步），发现新版本自动构建发布。
- **喵喵商店发布**：仅私有商店（MiaoMiao private store），不发布官方商店。

## 服务架构（与上游 compose 一致）

| 服务 | 说明 |
| --- | --- |
| postgres:18-alpine | 主数据库 |
| pgvector:pg18 | 语义记忆向量库 |
| migrate | 一次性数据库迁移（启动时运行） |
| server | 主服务（内嵌 containerd，agent 沙箱工作区） |
| channel | 消息渠道网关 |
| web | Web UI（nginx，入口 :8082） |

## 说明

- **数据卷**：`/lzcapp/var/memoh/data`（应用数据）、`postgres/`、`pgvector/`（数据库）
- **config.toml**：首次启动由 setup_script 自动生成到 `/lzcapp/var/memoh/config/config.toml`（admin 密码/JWT/数据库密码均为稳定随机值），可编辑后重启生效
- **默认管理员**：`admin`，密码见 config.toml 的 `[admin].password`
- **44 个模型 provider 预置配置**（Anthropic/OpenAI/Azure/DeepSeek 等），自带 API Key 即可在 UI 配置
- ⚠️ **server 为特权容器**（agent 沙箱工作区需要嵌套 containerd），需运行时放行 privileged；若环境不支持，Agent 的"独立计算机"功能不可用，对话/记忆功能正常
- WebRTC 桌面串流端口未映射（懒猫反向代理场景不需要）

## 所需 Secrets

| Secret | 说明 |
| --- | --- |
| `APPSTORE_URL` / `APPSTORE_TOKEN` | 喵喵商店 API 地址与发布令牌 |
| `APP_ID` | 可选 |
| `PRIVATE_STORE_GROUP_CODES` | 可选，私有分组码 |

## 许可

AGPL-3.0（上游）
