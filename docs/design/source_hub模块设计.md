# source_hub 模块设计

## 1. 概述

`source_hub` 是脚手架后端服务中的独立子模块，负责对接公司目标系统以及后续可能接入的其他来源系统，向上层业务提供统一的 `skill` 和 `mcp` 能力。

从能力边界上，`source_hub` 内部分为两条主链路：

- `skill`：查询、详情、包下载
- `mcp`：查询、详情、配置拉取、连通性测试

### 1.1 目标

- 提供统一的 `skill` 查询和包下载能力。
- 提供统一的 `mcp` 查询、配置拉取和连通性测试能力。
- 屏蔽不同来源系统的接口差异，输出统一模型。
- 为后续接入业界来源和其他内部来源保留扩展点。

### 1.2 职责边界

本模块负责：

- 查询 `skill`、`mcp` 列表与详情。
- 下载指定版本 `skill` 包。
- 拉取统一后的 `mcp` 配置。
- 对候选远端 `MCP` 执行连通性验证。

本模块不负责：

- 群组配置的保存、修改、加载、下发和生效。
- 用户、群组、权限管理。
- `skill` 的本地解压、安装和执行。
- 本地 `stdio`/`npx`/`uvx`/`docker` 型 `MCP` 的安装和启动。

本模块输出的是候选源数据、`skill` 包下载能力、统一后的 `mcp` 配置以及测试结果，不负责最终配置持久化和客户端运行态管理。

## 2. 整体设计

`source_hub` 位于上层业务模块与外部来源系统之间，承担统一接入层职责：对上提供按资源类型划分的能力接口，对下通过可插拔的 Provider 对接不同来源系统。

### 2.1 架构分层

```text
上层业务模块
    |
    v
SourceHubFacade
    |
    +-- SkillQueryService
    +-- SkillPackageService
    +-- MCPQueryService
    +-- MCPConfigService
    +-- MCPConnectivityService
            |
            +-- ParameterResolver
            +-- RemoteMCPVerifier
    |
    +-- ProviderRegistry
            |
            +-- CompanySkillProvider
            +-- CompanyMCPProvider
            +-- Future Providers...
```

整体上分为四层：

- 门面层：对上暴露统一入口。
- 资源服务层：按 `skill`、`mcp` 拆分能力编排。
- Provider 层：封装不同来源系统的接口调用和字段映射。
- 验证层：封装与来源无关的远端 `MCP` 测试逻辑。

### 2.2 多来源扩展

为避免当前实现与公司目标系统深度耦合，模块采用“上层按资源拆分，下层按来源适配”的结构。扩展原则如下：

- `skill` 和 `mcp` 在服务层分开设计，互不混用流程。
- 每个来源系统使用独立 Provider 实现，不共享来源侧 DTO。
- 服务层只依赖统一抽象，不依赖具体来源实现。
- 测试层只消费统一后的 `mcp` 端点模型，不直接读取来源原始响应。

建议保留两类 Provider 抽象：

```python
class SkillSourceProvider(Protocol):
    source_code: str

    async def list_skills(self, request: ListSkillsRequest) -> PageResult[SkillSummary]:
        ...

    async def get_skill_detail(self, request: GetSkillDetailRequest) -> SkillDetail:
        ...

    async def download_skill_package(self, request: DownloadSkillPackageRequest) -> SkillPackagePayload:
        ...


class MCPSourceProvider(Protocol):
    source_code: str

    async def list_mcps(self, request: ListMCPsRequest) -> PageResult[MCPSummary]:
        ...

    async def get_mcp_detail(self, request: GetMCPDetailRequest) -> MCPDetail:
        ...

    async def pull_mcp_config(self, request: PullMCPConfigRequest) -> MCPConfigTemplate:
        ...
```

## 3. 代码组织与文件结构设计

### 3.1 目录结构

代码目录从 `src/scaffolding_backend/source_hub/` 开始组织：

```text
src/scaffolding_backend/source_hub/
├── __init__.py                         # 模块包入口
├── facade.py                           # 模块统一对外入口
├── registry.py                         # Provider 注册、查找与路由
├── common/
│   ├── __init__.py                     # 公共能力包
│   ├── exceptions.py                   # 模块级异常定义
│   └── page.py                         # 分页等公共模型
├── skill/
│   ├── __init__.py                     # skill 资源能力包
│   ├── contracts.py                    # skill 对外契约
│   ├── models.py                       # skill 统一模型
│   ├── query_service.py                # skill 查询能力编排
│   └── package_service.py              # skill 包下载能力编排
├── mcp/
│   ├── __init__.py                     # mcp 资源能力包
│   ├── contracts.py                    # mcp 对外契约
│   ├── models.py                       # mcp 与端点统一模型
│   ├── query_service.py                # mcp 查询能力编排
│   ├── config_service.py               # mcp 配置拉取能力编排
│   ├── connectivity_service.py         # mcp 连通性测试能力编排
│   ├── parameter_resolver.py           # mcp 参数替换与模板解析
│   └── remote_mcp_verifier.py          # 远端 MCP 连通性验证
└── providers/
    ├── __init__.py                     # 来源 Provider 包
    ├── base.py                         # Provider 抽象接口定义
    └── company/
        ├── __init__.py                 # 公司目标系统来源实现
        ├── auth.py                     # 公司目标系统鉴权封装
        ├── client.py                   # 公司目标系统 HTTP 调用封装
        ├── skill_schemas.py            # 公司来源 skill DTO 定义
        ├── skill_mappers.py            # 公司 skill DTO 到统一模型的映射
        ├── skill_provider.py           # 公司来源 skill provider 实现
        ├── mcp_schemas.py              # 公司来源 mcp DTO 定义
        ├── mcp_mappers.py              # 公司 mcp DTO 到统一模型的映射
        └── mcp_provider.py             # 公司来源 mcp provider 实现
```

### 3.2 依赖关系

主要对象依赖关系如下：

```mermaid
classDiagram
    class SourceHubFacade
    class SkillQueryService
    class SkillPackageService
    class MCPQueryService
    class MCPConfigService
    class MCPConnectivityService
    class ProviderRegistry
    class SkillSourceProvider
    class MCPSourceProvider
    class CompanySkillProvider
    class CompanyMCPProvider
    class CompanyClient
    class CompanyAuth
    class SkillMappers
    class MCPMappers
    class ParameterResolver
    class RemoteMCPVerifier

    SourceHubFacade --> SkillQueryService : calls
    SourceHubFacade --> SkillPackageService : calls
    SourceHubFacade --> MCPQueryService : calls
    SourceHubFacade --> MCPConfigService : calls
    SourceHubFacade --> MCPConnectivityService : calls

    SkillQueryService --> ProviderRegistry : lookup provider
    SkillPackageService --> ProviderRegistry : lookup provider
    MCPQueryService --> ProviderRegistry : lookup provider
    MCPConfigService --> ProviderRegistry : lookup provider
    MCPConnectivityService --> ProviderRegistry : lookup provider

    MCPConnectivityService --> ParameterResolver : resolve params
    MCPConnectivityService --> RemoteMCPVerifier : verify endpoint

    ProviderRegistry --> SkillSourceProvider : returns
    ProviderRegistry --> MCPSourceProvider : returns

    CompanySkillProvider ..|> SkillSourceProvider : implements
    CompanyMCPProvider ..|> MCPSourceProvider : implements

    CompanySkillProvider --> CompanyClient : uses
    CompanySkillProvider --> SkillMappers : uses
    CompanyMCPProvider --> CompanyClient : uses
    CompanyMCPProvider --> MCPMappers : uses

    CompanyClient --> CompanyAuth : uses
```

### 3.3 模块对外接口

`source_hub` 对上层统一提供以下接口能力：

```python
class SourceHubFacade:
    async def list_skills(self, request: ListSkillsRequest) -> PageResult[SkillSummary]:
        ...

    async def get_skill_detail(self, request: GetSkillDetailRequest) -> SkillDetail:
        ...

    async def download_skill_package(self, request: DownloadSkillPackageRequest) -> SkillPackagePayload:
        ...

    async def list_mcps(self, request: ListMCPsRequest) -> PageResult[MCPSummary]:
        ...

    async def get_mcp_detail(self, request: GetMCPDetailRequest) -> MCPDetail:
        ...

    async def pull_mcp_config(self, request: PullMCPConfigRequest) -> MCPConfigTemplate:
        ...

    async def test_remote_mcp(self, request: TestRemoteMCPRequest) -> ConnectivityTestResult:
        ...
```

无论最终通过 HTTP、RPC 还是内部服务方式暴露，模块内部都应以 `SourceHubFacade` 作为统一入口，不直接对外暴露 Provider。

## 4. Skill 能力设计

当前版本 `skill` 侧依赖的公司目标系统接口文档为 [Skill 接口文档](./目标系统接口/Skill.md)。

### 4.1 Skill 查询能力

#### 4.1.1 查询边界

`skill` 查询链路保持轻量，只承担管理端或展示端所需的元数据查询能力：

- 列表阶段返回 `SkillSummary`
- 详情阶段返回 `SkillDetail`
- 不在查询阶段触发下载

#### 4.1.2 `list_skills`

依赖上游接口：`skills/v1/query`

```mermaid
sequenceDiagram
    participant Caller as 上层业务模块
    participant Hub as source_hub
    participant Provider as 公司 Skill Provider
    participant Upstream as 公司目标系统

    Caller->>Hub: list_skills(request)
    Hub->>Provider: list_skills(request)
    Provider->>Upstream: POST skills/v1/query
    Upstream-->>Provider: Skill 列表响应
    Provider-->>Hub: PageResult[SkillSummary]
    Hub-->>Caller: PageResult[SkillSummary]
```

关键字段引用范围：

| 类型 | 字段 |
| --- | --- |
| 请求 | `keyword`、`pageNum`、`pageSize`、`publishLevel` |
| 响应 | `skillId`、`name`、`latestDescription`、`latestVersion`、`tags`、`publisher` |

输出模型 `SkillSummary` 建议包含：

- `source_code`
- `skill_id`
- `name`
- `description`
- `version`
- `tags`
- `publisher`

关键字段映射：

| 输出字段 | 上游字段 |
| --- | --- |
| `skill_id` | `skillId` |
| `name` | `name` |
| `description` | `latestDescription` |
| `version` | `latestVersion` |
| `tags` | `tags[].name` |
| `publisher` | `publisher` |

#### 4.1.3 `get_skill_detail`

依赖上游接口：`skills/v1/get`

```mermaid
sequenceDiagram
    participant Caller as 上层业务模块
    participant Hub as source_hub
    participant Provider as 公司 Skill Provider
    participant Upstream as 公司目标系统

    Caller->>Hub: get_skill_detail(request)
    Hub->>Provider: get_skill_detail(request)
    Provider->>Upstream: GET skills/v1/get
    Upstream-->>Provider: Skill 详情响应
    Provider-->>Hub: SkillDetail
    Hub-->>Caller: SkillDetail
```

关键字段引用范围：

| 类型 | 字段 |
| --- | --- |
| 请求 | `skillId`、`version` |
| 响应 | `skillId`、`name`、`version`、`content`、`description`、`archiveUrl` |

输出模型 `SkillDetail` 在 `SkillSummary` 基础上增加：

- `content`
- `archive_url`
- `raw_ref`

关键字段映射：

| 输出字段 | 上游字段 |
| --- | --- |
| `skill_id` | `skillId` |
| `name` | `name` |
| `description` | `description` |
| `version` | `version` |
| `content` | `content` |
| `archive_url` | `archiveUrl` |

### 4.2 Skill 包下载能力

#### 4.2.1 下载边界

`download_skill_package` 用于补齐 `skill` 的最终使用闭环。配置管理模块在配置加载流程中调用该能力，从市场下载完整的 `skill` 包，供客户端后续加载和使用。

#### 4.2.2 `download_skill_package`

依赖上游接口：`skills/v1/download`

```mermaid
sequenceDiagram
    participant Caller as 配置管理模块
    participant Hub as source_hub
    participant Provider as 公司 Skill Provider
    participant Upstream as 公司目标系统

    Caller->>Hub: download_skill_package(request)
    Hub->>Provider: download_skill_package(request)
    Provider->>Upstream: POST skills/v1/download
    Upstream-->>Provider: file stream
    Provider-->>Hub: file stream
    Hub-->>Caller: file stream
```

关键字段引用范围：

| 类型 | 字段 |
| --- | --- |
| 请求 | `skillId`、`version` |
| 响应 | 文件流 |

#### 4.2.3 请求与返回

建议请求至少包含：

- `source_code`
- `skill_id`
- `version`

返回结果直接对应上游下载接口的文件流。

#### 4.2.4 作用说明

- 配置管理模块在配置加载流程中按需调用该能力。
- `source_hub` 当前版本直接代理上游 `skills/v1/download`。
- 返回结果为完整 `skill` 包内容，供客户端后续使用。

#### 4.2.5 错误处理

建议统一错误分类：

- `SourceNotSupportedError`
- `SourceAuthError`
- `SourceRequestError`
- `SourceResourceNotFoundError`
- `SkillPackageDownloadError`

## 5. MCP 能力设计

当前版本 `mcp` 侧依赖的公司目标系统接口文档为 [MCP 接口文档](./目标系统接口/MCP.md)。

### 5.1 MCP 查询能力

#### 5.1.1 查询边界

`mcp` 查询链路用于管理端候选项展示：

- 列表阶段返回 `MCPSummary`
- 详情阶段返回 `MCPDetail`
- 查询结果保留后续配置拉取和测试所需的定位信息

#### 5.1.2 `list_mcps`

依赖上游接口：`mcps/query`

```mermaid
sequenceDiagram
    participant Caller as 上层业务模块
    participant Hub as source_hub
    participant Provider as 公司 MCP Provider
    participant Upstream as 公司目标系统

    Caller->>Hub: list_mcps(request)
    Hub->>Provider: list_mcps(request)
    Provider->>Upstream: POST mcps/query
    Upstream-->>Provider: MCP 列表响应
    Provider-->>Hub: PageResult[MCPSummary]
    Hub-->>Caller: PageResult[MCPSummary]
```

关键字段引用范围：

| 类型 | 字段 |
| --- | --- |
| 请求 | `pageNum`、`pageSize`、`keyWord`、`categoryIdList`、`tagIdList`、`mcpMode` |
| 响应 | `manifestId`、`publisherId`、`name`、`description`、`version`、`tagList`、`categoryList`、`isLocal` |

输出模型 `MCPSummary` 建议包含：

- `source_code`
- `manifest_id`
- `publisher_id`
- `name`
- `description`
- `version`
- `tags`
- `categories`
- `is_local`

关键字段映射：

| 输出字段 | 上游字段 |
| --- | --- |
| `manifest_id` | `manifestId` |
| `publisher_id` | `publisherId` |
| `name` | `name` |
| `description` | `description` |
| `version` | `version` |
| `tags` | `tagList[].name` |
| `categories` | `categoryList[].name` |
| `is_local` | `isLocal` |

#### 5.1.3 `get_mcp_detail`

依赖上游接口：`mcps/{manifestId}/detail`

```mermaid
sequenceDiagram
    participant Caller as 上层业务模块
    participant Hub as source_hub
    participant Provider as 公司 MCP Provider
    participant Upstream as 公司目标系统

    Caller->>Hub: get_mcp_detail(request)
    Hub->>Provider: get_mcp_detail(request)
    Provider->>Upstream: GET mcps/{manifestId}/detail
    Upstream-->>Provider: MCP 详情响应
    Provider-->>Hub: MCPDetail
    Hub-->>Caller: MCPDetail
```

关键字段引用范围：

| 类型 | 字段 |
| --- | --- |
| 请求 | `manifestId`、`publisherId`、`version` |
| 响应 | `manifestId`、`publisherId`、`name`、`description`、`version`、`config.remoteList`、`config.remote`、`config.npx`、`config.uvx`、`config.docker`、`config.parameters` |

输出模型 `MCPDetail` 在 `MCPSummary` 基础上增加：

- `content`
- `demands`
- `profiles`
- `raw_ref`

关键字段映射：

| 输出字段 | 上游字段 |
| --- | --- |
| `manifest_id` | `manifestId` |
| `publisher_id` | `publisherId` |
| `name` | `name` |
| `description` | `description` |
| `version` | `version` |
| `profiles` | `config.remoteList` / `config.remote` / `config.npx` / `config.uvx` / `config.docker` |

### 5.2 MCP 配置拉取能力

#### 5.2.1 能力定位

`pull_mcp_config` 用于从来源侧详情中抽取并生成统一后的运行配置模板，供上层业务保存或进一步加工。这里的重点不是只返回远端端点，而是返回客户端可消费的 `MCP` 配置 profile 列表。

#### 5.2.2 `pull_mcp_config`

依赖上游接口：`mcps/{manifestId}/detail`

```mermaid
sequenceDiagram
    participant Caller as 上层业务模块
    participant Hub as source_hub
    participant Provider as 公司 MCP Provider
    participant Upstream as 公司目标系统

    Caller->>Hub: pull_mcp_config(request)
    Hub->>Provider: pull_mcp_config(request)
    Provider->>Upstream: GET mcps/{manifestId}/detail
    Upstream-->>Provider: MCP 详情响应
    Provider-->>Hub: MCPConfigTemplate
    Hub-->>Caller: MCPConfigTemplate
```

关键字段引用范围：

| 类型 | 字段 |
| --- | --- |
| 请求 | `manifestId`、`publisherId`、`version` |
| 响应 | `config.parameters`、`config.remoteList`、`config.remote`、`config.npx`、`config.uvx`、`config.docker`、`config.packageDownloadUrl`、`config.expireIn` |

#### 5.2.3 输出模型

`MCPConfigTemplate` 建议至少包含：

- `source_code`
- `manifest_id`
- `publisher_id`
- `version`
- `profiles`
- `parameter_definitions`
- `metadata`

其中 `profiles` 为统一后的运行配置列表，`parameter_definitions` 为后续配置填写和测试所需的参数定义。

建议的统一模型如下：

```python
class MCPConfigTemplate:
    source_code: str
    manifest_id: str
    publisher_id: str
    version: str
    profiles: list[MCPProfile]
    parameter_definitions: dict[str, ParameterDefinition]
    metadata: dict[str, Any]


class MCPProfile:
    name: str
    mode: Literal["remote", "local", "bootstrap_remote"]
    launcher: Literal["remote", "npx", "uvx", "docker", "custom"] | None
    mcp_server_config: dict[str, Any]
    prestart_command: str | None
```

#### 5.2.4 Profile 抽取规则

来源侧 `mcp` 详情中，统一按运行配置 profile 抽取：

- `config.remoteList` 中的每个对象抽取为一个 profile。
- 若不存在 `remoteList` 但存在 `remote`，则用 `remote` 构建单个 profile。
- `config.npx`、`config.uvx`、`config.docker` 分别抽取为本地运行 profile。
- profile 中的 `mcp_server_config` 统一来自对应节点下的 `config.mcpServers`。

当前版本建议按以下规则识别 profile 类型：

- 仅包含 `url/type/headers` 的远端配置，识别为 `mode=remote`
- 包含 `command/args/env` 的本地运行配置，识别为 `mode=local`
- `remote` 配置中同时包含 `url/type` 和 `command` 的场景，识别为 `mode=bootstrap_remote`

#### 5.2.5 Profile 应用说明

对调用方而言，`profiles` 表示同一个 `MCP` 在当前版本下可供客户端使用的不同运行方式：

- `remote`：客户端可直接按 URL 连接
- `local`：客户端按 `command/args/env` 启动本地 `MCP`
- `bootstrap_remote`：客户端需先执行预启动命令，再通过远端 URL 连接

`mcpServers` 字段在三类 profile 中都表示客户端最终消费的运行配置片段，不只是安装信息：

- 远端 profile 中，`mcpServers` 主要提供 `url/type/headers`
- 本地 profile 中，`mcpServers` 主要提供 `command/args/env`
- `bootstrap_remote` profile 中，`mcpServers` 提供最终连接 URL，而 `prestart_command` 提供预启动能力

### 5.3 MCP 连通性测试能力

#### 5.3.1 当前测试范围

本模块当前仅支持一种测试能力：

- `mode=remote` 的远端 `MCP` 服务连通性验证

#### 5.3.2 `test_remote_mcp`

依赖上游接口：复用 `mcps/{manifestId}/detail`

```mermaid
sequenceDiagram
    participant Caller as 上层业务模块
    participant Hub as source_hub
    participant Provider as 公司 MCP Provider
    participant Upstream as 公司目标系统

    Caller->>Hub: test_remote_mcp(request)
    Hub->>Provider: pull_mcp_config(request)
    Provider->>Upstream: GET mcps/{manifestId}/detail
    Upstream-->>Provider: MCP 详情响应
    Provider-->>Hub: MCPConfigTemplate
    Hub->>Hub: 参数解析与连通性验证
    Hub-->>Caller: ConnectivityTestResult
```

测试链路内部优先复用 `pull_mcp_config` 的结果，不直接在测试层处理来源侧原始 DTO。

#### 5.3.3 测试请求

```python
class TestRemoteMCPRequest:
    source_code: str
    manifest_id: str
    publisher_id: str
    version: str | None
    profile_name: str | None
    parameter_values: dict[str, str]
```

#### 5.3.4 参数解析

`parameter_resolver.py` 负责处理如下逻辑：

- 合并来源默认值与调用方输入值
- 校验必填参数是否齐全
- 对 `url_template`、`headers_template` 中的 `{{PARAM}}` 占位符进行替换
- 对敏感参数执行日志脱敏

#### 5.3.5 连通性验证分层

远端 `MCP` 测试分为四层：

1. 输入校验  
   校验 profile 是否存在、`mode` 是否为 `remote`、URL 格式是否正确。

2. 地址可达性校验  
   校验目标地址是否可连接，超时是否合理。

3. MCP 协议初始化  
   与远端服务建立 MCP 会话，完成 `initialize`。

4. 工具列表探测  
   尝试获取工具列表，确认服务可正常提供能力。

#### 5.3.6 Unsupported 场景

以下场景直接返回 `UNSUPPORTED`：

- profile 不存在
- profile 的 `mode` 不为 `remote`
- profile 的 `transport` 不属于 `sse`、`streamable-http`

#### 5.3.7 结果模型

```python
class ConnectivityTestResult:
    success: bool
    status: str
    phase: str
    profile_name: str | None
    resolved_url: str | None
    latency_ms: int | None
    tool_count: int | None
    message: str
    diagnostics: dict[str, Any]
```

#### 5.3.8 错误处理

建议统一错误分类：

- `UnsupportedTestTargetError`
- `TestParameterMissingError`
- `TestParameterResolveError`
- `TestConnectTimeoutError`
- `TestConnectFailedError`
- `TestProtocolInitError`
- `TestToolsListError`

## 6. 后续扩展建议

为保证后续演进平滑，建议在当前版本预留以下扩展点：

- 新来源系统 Provider 扩展
- `skill` 包下载缓存或分发能力扩展
- `mcp` 本地运行配置支持扩展
- 统一缓存层扩展
- 查询结果排序和筛选字段扩展
- 测试结果审计记录扩展

当前版本实现时，优先保证以下原则：

- 结构上支持多来源，但只落地公司目标系统 Provider
- `skill` 与 `mcp` 在服务层彻底拆开
- `skill` 只做查询与包下载，不承担安装职责
- `mcp` 只做查询、配置拉取和远端测试，不在后端执行本地命令型安装或启动逻辑
