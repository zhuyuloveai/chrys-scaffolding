## MCP注册接口

[MCP注册外部接口文档](https://wiki.huawei.com/domains/933/wiki/117416/WIKI202509048151932)

## 接口鉴权

### apig鉴权

apig鉴权见：[apig请求方法](https://his-beta.huawei.com/doc/#/page.html?service_code=hrn:hic:servicemarket::service:apigw&group_id=bce14fb2eda34368917bf22dddaaf78b&lang=zh_CN)

接口异常响应示例：[API网关错误返回说明](https://his-beta.huawei.com/doc/#/page.html?service_code=hrn:hic:servicemarket::service:apigw&group_id=28a3c933a23a4054b054035824866835&lang=zh_CN)

### 云龙token

请求头携带云龙token参数

| header key | value |
|--------|------|
| X-AUTH-TOKEN | 云龙token  |

异常响应示例

```json
{
    "error_code": "DEV.00000003",
    "error_msg": "认证信息过期"
}
```

鉴权异常码说明

| httpCode | error_code | errir_msg | 说明 |
|--------|------|------|------|
| 401 | DEV.00000001 | 未识别到用户认证信息 | 未传token |
| 401 | DEV.00000002 | 认证信息无法识别  | token错误 |
| 401 | DEV.00000003 | 认证信息过期 | token过期 |

## 业务异常码

异常响应示例

```json
{
    "code": 400,
    "message": "趋势只能为FEATURED,OFFICIAL,HOSTED,FAVORITE"
}
```

鉴权异常码说明

| httpCode | code | message | 说明 |
|--------|------|------|------|
| 200 | 400 | message字段为具体业务异常信息 | 业务异常 |
| 200 | 500 | 系统内部错误，请联系运维解决 | 未知异常 |

## 获取MCP列表接口

分页查询mcpServer列表

### 请求地址

请求方法 POST

| 环境 | apig地址 | 服务地址 |
|--------|------|------|
| **alpha** | https://apigw-beta.huawei.com/api/alpha/external/v1/mcps/query | 
| **beta** | https://apigw-beta.huawei.com/api/external/v1/mcps/query |
| **prod** |https://apigw-cn-south02.huawei.com/api/mcpService/external/v1/mcps/query|

### 请求参数

ContentType: application/json

| 参数名 | 类型 | 是否必填 |示例值 | 描述 |
|--------|------|--------|------|------|
| **pageSize** | int | 否 | 10 | 请求分页大小，默认值10，最大值500 |
| **pageNum** | int | 否 | 1 | 当前分页，默认值1 |
| **trend** | enum | 否 | "FEATURED" | 趋势，枚举字段，FEATURED：精选服务器，OFFICIAL：官方服务器，LATEST：最新服务器, FAVORITE: 我的收藏 |
| **keyWord** | string | 否 | "file" | 按名称的模糊搜索关键字 |
| **categoryIdList** | array[string]| 否 | [1] | mcp市场分类主键id列表,string类型的long值 |
| **tagIdList** | array[string] | 否 | [1,2] | mcp市场标签主键id列表,string类型的long值 |
| **publisherId** | string（long值的string，非数字类型string会报错） | 否 | 123333 | 发布商id |
| **mcpMode** | int| 否 | 0 - local, 1 - remote | 安装方式 |
| **isOfficial** | long| 否 | 1 | 官方推荐类型：1-CodeMate官方推荐，2-DevMate官方推荐，3-TestMate官方推荐，4-BuildMate官方推荐，5-MCP市场官方推荐 |
| **createBy** | string| 否 | zhangsan 123456789 | 创建人 |
| **sortBy** | string| 否 | installation | 榜单类型（排序方式）：installation-安装榜 comment-好评榜 |
| **favoriteUser** | string| 否 | zhangsan 123456789 | 收藏用户Id,搭配tread=FAVORITE使用 |
| **advancedTagQuery** | list| 否 | [[1],[2]] | 对tag用AND的逻辑查询mcp列表 |

请求参数示例

```json
{
    "pageSize": 50,
    "pageNum": 1,
    "trend": "LATEST",
    "keyWord": "file",
    "categoryIdList": ["1"],
    "tagIdList": ["1", "2"],
    "publisherId": "123333",
    "isOfficial": 1,
    "createBy": "zhangsan 123456789"，
    "sortBy": "installation",
    "advancedTagQuery": [[1],[2]]
}
```

### 接口响应示例

```json
{
    "code": 200,
    "message": "OK",
    "data": {
        "pageNum": 1,
        "pageSize": 10,
        "total": 19,
        "data": [
            {
                "id": "210",
                "uuid": "Codehub-Mcp-Server",
                "manifestId": "Codehub-Mcp-Server",
                "publisherId": "1",
                "name": "Codehub-Mcp-Server",
                "aliasName": null,
                "description": "A Model Context Protocol server for Git repository interaction and automation.",
                "iconUrl": "https://s3-kp-kwe.his-beta.huawei.com:443/mcp-market-bucket-alpha/images/fa97389f23c44367b7dda9c71d0388c1/logo.png",
                "repositoryUrl": "https://codehub-g.huawei.com/RepoAI/MCP/codearts-mcp-server/files?ref=master",
                "tagList": [
                    {
                        "id": "191",
                        "name": "selfdeveloped"
                    },
                    {
                        "id": "194",
                        "name": "codehub"
                    }
                ],
                "categoryList": [
                    {
                        "id": "1",
                        "name": "开发者工具"
                    }
                ],
                "version": "0.0.1",
                "versionAttr": "lts",
                "isLocal": true,
                "isSelfDeveloped": true,
                "downloadNum": null,
                "createTime": "2025-07-05 17:41:13",
                "createBy": "pujieming 00925763",
                "updateTime": "2025-07-05 17:43:21",
                "updateBy": null,
                "isHosted": null,
                "isFeatured": null,
                "isOfficial": null
            },
            ...
        ]
    }
}

```

### 响应字段说明

| 字段名称 | 类型    | 是否必须 | 描述                                       |
| ---------- | --------- | ---------- | -------------------------------------------- |
| code     | integer | 是       | 响应状态码，200 表示成功。                 |
| message  | string  | 是       | 响应消息，"OK" 表示成功。           |
| data     | object  | 是       | 响应数据对象。 |
| data.pageNum  | integer | 是       | 当前页码。                   |
| data.pageSize | integer | 是       | 每页显示的数据条数。         |
| data.total    | integer | 是       | 总数据条数。                 |
| data.data     | array[McpVO]   | 是       | 数据列表，包含多个数据对象。 |

McpVO响应字段说明

| 字段名称        | 类型    | 是否必须 | 描述                                     |
| ----------------- | --------- | ---------- | ------------------------------------------ |
| id              | string | 是       | 主键id, string格式long值                         |
| uuid            | string  | 是       |  同发布商下mcpServer唯一id，推荐使用manifestId    |
| manifestId            | string  | 是       |  同发布商下mcpServer唯一id    |
| publisherId            | string  | 是       |  发布商id    |
| name            | string  | 是       | mcpServer 名称  |
| aliasName       | string  | 否       | mcpServer别名  |
| description     | string  | 是       | server描述 |
| iconUrl         | string  | 是       | 图标URL |
| repositoryUrl   | string  | 是       | 代码仓库 URL。                         |
| tagList         | array[TagVO]   | 是       | 数据的标签列表    |
| categoryList    | array[CategoryVO]   | 是       | 数据的分类列表     |
| isLocal         | boolean | 是       | 是否为本地运行                      |
| version            | string  | 是       | 版本号  |
| versionAttr           | string  | 是       | 版本属性 |
| isSelfDeveloped | boolean | 是       | 是否为自研                    |
| downloadNum     | integer | 否       | 下载次数        |
| createTime      | string  | 是       | 创建时间，格式为 "yyyy-MM-dd HH:mm:ss"。 |
| createBy        | string  | 否       | 创建者                         |
| updateTime      | string  | 是       | 更新时间，格式为 "yyyy-MM-dd HH:mm:ss"。 |
| updateBy        | string  | 否       | 更新者                      |
| isHosted        | boolean | 否       | 是否托管                 |
| isFeatured      | boolean | 否       | 是否为精选                 |
| isOfficial      | boolean | 否       | 是否为官方         |

TagVO响应字段说明

| 字段名称 | 类型    | 是否必须 | 描述                                       |
| ---------- | --------- | ---------- | -------------------------------------------- |
| id | string | 是       | 标签id |
| name | string | 是       | 标签名称 |

CategoryVO响应字段说明

| 字段名称 | 类型    | 是否必须 | 描述                                       |
| ---------- | --------- | ---------- | -------------------------------------------- |
| id | string| 是       | 分类id |
| name | string | 是       | 分类名称 |

## 获取mcp详情接口

通过详情接口，获取mcp server基本信息和启动运行的命令及参数信息。

### 请求地址

请求方法 GET

| 环境 | 地址 |
|--------|------|
| **alpha** | https://apigw-beta.huawei.com/api/alpha/external/v1/mcps/{manifestId}/detail |
| **beta** | https://apigw-beta.huawei.com/api/external/v1/mcps/{manifestId}/detail |
| **prod** |https://apigw-cn-south02.huawei.com/api/external/v1/mcps/{manifestId}/detail|

### 请求参数

path参数

| 参数名 | 类型 | 是否必填 |示例值 | 描述 |
|--------|------|--------|------|------|
| **manifestId** | string | 是 |file-system | 同发布商下mcpServer唯一id|

query参数

| 参数名 | 类型 | 是否必填 |示例值 | 描述 |
|--------|------|--------|------|------|
| **version** | string | 否 |0.0.1 | 版本号|
| **publisherId** | string | 是 |1 | 发布商id|

### 接口响应示例

```json
response: response: {
    "code": 200,
    "message": "OK",
    "data": {
        "id": "140",
        "manifestId": "filesystemMcp",
        "publisherId": "1",
        "name": "filesystemMcp",
        "aliasName": null,
        "description": "filesystemMcp工具",
        "iconUrl": "http://example.com/icon.png",
        "repositoryUrl": null,
        "tagList": [
            "opensource"
        ],
        "categoryList": [],
        "content": "# filesystemMcp \n文件系统Mcp助手",
        "demand": [
            {
                "mcpProtocol": "version/241105"
            },
            {
                "engines": {
                    "node": ">18.1 <20"
                }
            },
            {
                "os": [
                    "linux",
                    "darwin"
                ]
            },
            {
                "cpu": [
                    "arm"
                ]
            }
        ],
        "config": {
            "name": "filesystemMcp",
            "author": "",
            "description": "filesystemMcp",
            "version": "0.1.0",
            "parameters": {
                "USER_DIR": {
                    "description": "用户路径",
                    "type": "string",
                    "required": true,
                    "default": "/%USERPROFILE%"
                }
            },
            "remoteList": [
                {
                    "name": "filesystemMcp",
                    "type": "sse",
                    "url": "http://localhost:{{USER_PORT}}/sse",
                    "command": "uvx --allow-insecure-host https://s3-kp-kwe.his-beta.huawei.com --from 'https://s3-kp-kwe.his-beta.huawei.com/mcp-market-bucket-beta/public/package/7e7edeef40524117903cbc67a5e8323a.tar.gz'",
                    "config": {
                        "mcpServers": {
                            "filesystemMcp": {
                                "url": "http://localhost:{{USER_PORT}}/sse",
                                "type": "sse",
                                "timeout": 300,
                                "headers": {"key1":"val1","key2":"val2"}
                            }
                        }
                    },
                    "parameters": {
                        "USER_PORT": {
                            "description": "用户指定本地运行的端口号",
                            "type": "int",
                            "required": true,
                            "default": "8000"
                        }
                    }
                }
            ],
            "remote": {
                "name": "filesystemMcp",
                "type": "sse",
                "url": "http://localhost:{{USER_PORT}}/sse",
                "command": "uvx --allow-insecure-host https://s3-kp-kwe.his-beta.huawei.com --from 'https://s3-kp-kwe.his-beta.huawei.com/mcp-market-bucket-beta/public/package/7e7edeef40524117903cbc67a5e8323a.tar.gz'",
                "config": {
                    "mcpServers": {
                        "filesystemMcp": {
                            "url": "http://localhost:{{USER_PORT}}/sse",
                            "type": "sse",
                             "timeout": 300,
                             "headers": {"key1":"val1","key2":"val2"}
                        }
                    }
                },
                "parameters": {
                    "USER_PORT": {
                        "description": "用户指定本地运行的端口号",
                        "type": "int",
                        "required": true,
                        "default": "8000"
                    }
                }
            },
            "npx": null,
            "uvx": {
                "command": "uvx",
                "args": [
                    "--allow-insecure-host",
                    "https://s3-kp-kwe.his-beta.huawei.com",
                    "--from",
                    "'https://s3-kp-kwe.his-beta.huawei.com/mcp-market-bucket-beta/public/package/7e7edeef40524117903cbc67a5e8323a.tar.gz'",
                    "{{USER_DIR}}"
                ],
                "config": {
                    "mcpServers": {
                        "filesystemMcp": {
                            "command": "uvx",
                            "args": [
                                "--allow-insecure-host",
                                "https://s3-kp-kwe.his-beta.huawei.com",
                                "--from",
                                "'https://s3-kp-kwe.his-beta.huawei.com/mcp-market-bucket-beta/public/package/7e7edeef40524117903cbc67a5e8323a.tar.gz'",
                                "{{USER_DIR}}"
                            ],
                            "timeout": 300,
                            "headers": {"key1":"val1","key2":"val2"}
                        }
                    }
                }
            },
            "docker": null
        }
    }
}
```

### 响应字段说明

| 字段名 | 类型 | 描述 |
|--------|------|------|
| **code** | integer | 响应状态码，200表示成功。 |
| **message** | string | 响应消息，通常为"OK"。 |
| **data** | object | 响应数据对象，包含以下字段： |

#### data 字段

| 字段名 | 类型 | 是否必须 | 描述 |
|--------|------| ------ |------|
| **id** | string|是| 记录的唯一标识。string格式long值 |
| **manifestId**  | string  | 是       |  同发布商下mcpServer唯一id    |
| **publisherId** | string| 是       |  发布商id    |
| **name** | string |是|  mcp名称 |
| **aliasName** | string| 是 | 别名 |
| **description** | string | 否 | 工具描述。 |
| **iconUrl** | string | 否 | 图标URL |
| **tagList** | array[string] | 是 | 标签列表|
| **categoryList** | array[string] | 否 | 分类列表。 |
| **content** | string |否| 详情描述。 |
| **demand** | array[demand] |否|  需求列表，mcp运行环境要求 |
| **demand.mcpProtocol** | string |否| mcpServer支持的mcp协议版本，无法在不支持此版本的mcpClient中运行 |
| **demand.engines** | object |否| 执行引擎要求  |
| **demand.engines.node** | string |否| node版本要求  |
| **demand.engines.python** | string |否| python版本要求  |
| **demand.os** | array[string] |否| 操作系统要求,指定运行操作系统。"!systemName"格式可指定不支持的操作系统 |
| **demand.cpu** | array[string] |否| cpu架构要求，指定运行所需的cpu架构信息。"!archName"格式指定不支持的架构  |
| **config** | object |是| 配置信息，包含以下字段： |
| **config.name** | string |是| mcp server 名称 |
| **config.author** | string |否| mcp 作者 |
| **config.description** | string |否| mcp的描述 |
| **config.version** | string |是| MCP协议版本号 |
| **config.parameters** | map<string, paramter> |否| 参数配置，map结构，key为参数名称，在命令使用参数的地方提供参数名称的占位符{{}}，value为参数对象，参数对象包含以下字段： |
| **config.parameters.parameter.description** | string | 否 | 参数描述。 |
| **config.parameters.parameter.type** | string | 否 | 参数类型,空值默认字符串。enum值（string,int,boolean） |
| **config.parameters.parameter.required** | boolean | 否 | 执行命令是否需要参数，true需设置 |
| **config.parameters.parameter.default** | string | 否 | 提供参数默认值 |
| **config.parameters.parameter.sensitive** | boolean | 否 | 是否敏感信息 |
| **config.remoteList** | array[remote]| 否 | 远程服务器配置列表，列表对象结构同下remote对象，mcp可能存在多个remote类型的配置，用列表返回 |
| **config.remote** | object | 否 | 远程服务器配置，当mcp有多个远程类型配置时，remote对象为remoteList中的首个，包含以下字段： |
| **config.remote.name** | string | 否 | 远程server名称。 |
| **config.remote.type** | string | 是 |远程server的连接方式，只有两个值二选一。（sse, streamable-http） |
| **config.remote.url** | string | 是 |远程server的url |
| **config.remote.command** | string | 否 | 本地启动http方式的server的命令，command属性不为空，则为remote混合模式 |
| **config.remote.config** | string | 否 | 本地启动http方式的server的命令配置 |
| **config.remote.config.mcpServers** | map<string, object> | 是 | map结构，key为mcp的manifestId，value为参数对象，参数对象包含以下字段： |
| **config.remote.config.mcpServers.url** | string | 是 | 远程server的url |
| **config.remote.config.mcpServers.type** | string | 是 | 远程server的连接方式 |
| **config.remote.config.mcpServers.timeout** | int | 否 | 超时时间 |
| **config.remote.config.mcpServers.headers** |Map<string, object>| 否 | headers信息 |
| **config.remote.parameters** | map<string, parameter> | 否 | 参数类型同config.parameters, 远程server的参数配置信息 |
| **config.remote.headers** | map<string, string> | 否 | 连接远程mcp的http headers信息 |
| **config.npx** | ServerConfig对象 | 否 | npx配置，字段详情见下文ServerConfig对象 |
| **config.uvx** | ServerConfig对象 | 否 | uvx配置，字段详情见下文ServerConfig对象 |
| **config.docker** | ServerConfig对象 | 否 | Docker配置, 字段详情见下文ServerConfig对象 |
| **config.packageDownloadUrl** | string | 否 | 包临时下载地址， |
| **config.expireIn** | long | 否 | 包临时下载链接过期时间 |

#### ServerConfig对象

| 字段名 | 类型 | 是否必须 | 描述 |
|--------|------|------|------|
| **command** | string | 是 | 执行命令。 |
| **args** | array[string] | 否 | 命令参数设置 |
| **env** | map<string,string> | 否 | 命令执行的环境变量设置 |
| **parameters** | map<string,parameter> | 否 | 参数类型同config.parameters, local server的参数配置信息 |
| **config** | map<string,object> | 否 | agent集成配置对象 |
| **config.mcpServers** | map<string, serverConfig> | 是 | map结构，key为mcp的manifestId，value为参数对象，参数对象包含以下字段： |
| **config.mcpServers.command** | string | 是 | mcp启动执行命令 |
| **config.mcpServers.description** | string | 是 | mcp描述 |
| **config.mcpServers.args** | array[string] | 否 | 执行的命令行参数信息 |
| **config.mcpServers.env** | map<string, string> | 是 | 环境变量信息 |
| **config.mcpServers.timeout** | int | 否 | 超时时间 |
| **config.mcpServers.headers** |Map<string, object>| 否 | headers信息 |

## mcp下载完成打点回调接口

通过打点回调接口，记录队形mcp server的下载次数。

### 请求地址

请求方法 POST

| 环境 | 地址 |
|--------|------|
| **alpha** | https://apigw-beta.huawei.com/api/alpha/external/v1/mcps/download/complete |
| **beta** | https://apigw-beta.huawei.com/api/external/v1/mcps/download/complete |
| **prod** |https://apigw-cn-south02.huawei.com/api/mcpService/external/v1/mcps/download/complete |

### 请求参数

ContentType: application/json

| 参数名 | 类型 | 是否必填 |示例值 | 描述 |
|--------|------|--------|------|------|
| **mcpId** | string | 是 | 1 | mcp server id|
| **userId** | strring | 是 |z00000001 | 下载用户，首字母+工号 |
| **source** | strring | 是 | codeMate | 集成侧系统标识，请联系mcp市场人员提供 |

请求参数示例

```json
{
    "mcpId": "1",
    "userId": "z00000001",
    "source": "codeMate"
}
```

### 接口响应示例

```json
response: 
{
    "code": 200,
    "message": "OK",
    "data": true
}
```

### 响应字段说明

| 字段名 | 类型 | 描述 |
|--------|------|------|
| **code** | integer | 响应状态码，200表示成功。 |
| **message** | string | 响应消息，通常为"OK"。 |
| **data** | boolean | true:成功，false:失败 |

## mcp市场分类信息列表接口

市场所有定义的分类信息列表

### 请求地址

请求方法 GET

| 环境 | 地址 |
|--------|------|
| **alpha** | https://apigw-beta.huawei.com/api/alpha/external/v1/mcps/categories |
| **beta** | https://apigw-beta.huawei.com/api/external/v1/mcps/categories |
| **prod** |https://apigw-cn-south02.huawei.com/api/mcpService/external/v1/mcps/categories |

无请求参数

### 接口响应示例

```json
response: {
    "code": 200,
    "message": "OK",
    "data": [
        {
            "id": "1",
            "name": "开发者工具"
        },
        {
            "id": "2",
            "name": "数据库"
        },
        {
            "id": "3",
            "name": "操作系统自动化"
        },
        {
            "id": "4",
            "name": "浏览器自动化"
        },
        {
            "id": "5",
            "name": "文件系统"
        },
        {
            "id": "6",
            "name": "知识与记忆"
        },
        {
            "id": "7",
            "name": "监控"
        },
        {
            "id": "9",
            "name": "安全"
        }
    ]
}
```

### 响应字段说明

| 字段名 | 类型 | 描述 |
|--------|------|------|
| **code** | integer | 响应状态码，200表示成功。 |
| **message** | string | 响应消息，通常为"OK"。 |
| **data** | array[object]| 分类数据列表 |
| **data.$item.id** | string类型的long值| 分类id |
| **data.$item.name** | string类型的long值| 分类名称 |

## mcp市场标签信息列表接口

市场所有定义的标签信息列表

### 请求地址

请求方法 GET

| 环境 | 地址 |
|--------|------|
| **alpha** | https://apigw-beta.huawei.com/api/alpha/mcpService/external/v1/mcps/tags |
| **beta** | https://apigw-beta.huawei.com/api/mcpService/external/v1/mcps/tags |
| **prod** |https://apigw-cn-south02.huawei.com/api/mcpService/external/v1/mcps/tags|

无请求参数

### 接口响应示例

```json
{
    "code": 200,
    "errorCode": null,
    "message": "OK",
    "data": [
        {
            "id": "1",
            "name": "研发工具生产线"
        },
        {
            "id": "26",
            "name": "mcp"
        },
        {
            "id": "27",
            "name": "filesystem"
        },
        {
            "id": "190",
            "name": "opensource"
        },
        {
            "id": "191",
            "name": "selfdeveloped"
        },
        {
            "id": "192",
            "name": "weather"
        },
        {
            "id": "193",
            "name": "mcpTimeServer"
        },
        {
            "id": "194",
            "name": "codehub"
        },
        {
            "id": "203",
            "name": "playwright"
        },
        {
            "id": "204",
            "name": "docker"
        },
        {
            "id": "205",
            "name": "JavaScript"
        },
        {
            "id": "206",
            "name": "黄区"
        }
    ]
}
```

### 响应字段说明

| 字段名 | 类型 | 描述 |
|--------|------|------|
| **code** | integer | 响应状态码，200表示成功。 |
| **message** | string | 响应消息，通常为"OK"。 |
| **data** | array[object]| 标签数据列表 |
| **data.$item.id** | string| 标签id，string类型long值 |
| **data.$item.name** | string| 标签名称 |

## mcp市场获取工具列表接口

市场获取工具列表

### 请求地址

请求方法 GET

| 环境 | 地址 |
|--------|------|
| **alpha** | https://apigw-beta.huawei.com/api/alpha/external/v1/mcps/${id}/tools |
| **beta** | https://apigw-beta.huawei.com/api/external/v1/mcps/${id}/tools |
| **prod** | https://apigw-cn-south02.huawei.com/api/mcpService/external/v1/mcps/${id}/tools |

无请求参数

### 接口响应示例

```json
response: {
    "code": 200,
    "message": "OK",
    "data": [
        {
            "id": "466",
            "serverId": "118",
            "toolName": "read_file",
            "description": "Read the complete contents of a file from the file system. Handles various text encodings and provides detailed error messages if the file cannot be read. Use this tool when you need to examine the contents of a single file. Only works within allowed directories.",
            "inputSchema": {
                "type": "object",
                "required": [
                    "path"
                ],
                "properties": {
                    "path": {
                        "type": "string"
                    }
                },
                "additionalProperties": false
            },
            "createTime": "2025-07-14 20:03:09",
            "createBy": "lvwei 30076405",
            "updateTime": "2025-07-14 20:03:09",
            "updateBy": "lvwei 30076405"
        },
        {
            "id": "467",
            "serverId": "118",
            "toolName": "read_multiple_files",
            "description": "Read the contents of multiple files simultaneously. This is more efficient than reading files one by one when you need to analyze or compare multiple files. Each file's content is returned with its path as a reference. Failed reads for individual files won't stop the entire operation. Only works within allowed directories.",
            "inputSchema": {
                "type": "object",
                "required": [
                    "paths"
                ],
                "properties": {
                    "paths": {
                        "type": "array",
                        "items": {
                            "type": "string"
                        }
                    }
                },
                "additionalProperties": false
            },
            "createTime": "2025-07-14 20:03:09",
            "createBy": "lvwei 30076405",
            "updateTime": "2025-07-14 20:03:09",
            "updateBy": "lvwei 30076405"
        }
    ]
}
```

### 响应字段说明

| 字段名 | 类型 | 描述 |
|--------|------|------|
| code | integer | 响应状态码，200 表示成功 |
| message | string | 响应消息，通常为 "OK" |
| data | array<object> | 工具列表 |
| data.$item.id | string | 工具唯一标识 string类型的long值|
| data.$item.serverId | string | 所属服务器标识 |
| data.$item.toolName | string | 工具名称 |
| data.$item.description | string | 工具功能描述 |
| data.$item.inputSchema | object | 工具入参 JSON Schema |
| data.$item.inputSchema.type | string | Schema 类型，固定为 "object" |
| data.$item.inputSchema.required | array<string> | 必填属性列表 |
| data.$item.inputSchema.properties | object | 各属性的定义（包括 type、items 等） |
| data.$item.createTime | string | 创建时间（格式：yyyy-MM-dd HH:mm:ss） |
| data.$item.createBy | string | 创建人信息 |
| data.$item.updateTime | string | 最近更新时间（格式同上） |
| data.$item.updateBy | string | 最近更新人信息 |

## 根据mcpId和version获取mcp详情接口

### 请求地址

请求方法 POST

| 环境 | 地址 |
|--------|------|
| **beta** | https://apigw-beta.huawei.com/api/mcpService/external/mcp/details/v1/get |
| **prod** | https://apigw-cn-south02.huawei.com/api/mcpService/external/mcp/details/v1/get |

### 请求参数

path参数

| 参数名 | 类型 | 是否必填 |示例值 | 描述 |
|--------|------|--------|------|------|
| **mcpId** | string | 是 |file-system | mcpServer唯一id |
| **version** | string | 是 |0.0.1 | 版本号 |

### 接口响应示例

```json
{
  "code": 200,
  "errorCode": null,
  "message": "OK",
  "data": {
    "id": "2031252533461237761",
    "mcpId": "fault-tree-analysis-mcp",
    "manifestId": "fault-tree-analysis-mcp",
    "publisherId": "1",
    "name": "fault-tree-analysis-mcp",
    "description": "A Model Context Protocol (MCP) server that execute fault tree analysis step.",
    "tagList": [
      "opensource",
      "DevMate"
    ],
    "categoryList": [
      "开发者工具"
    ],
    "content": "# Fault Tree Analysis Server\n\nMCP server for fault tree analysis and reliability calculations.\n\n## Usage\n\n```bash\ncd mcp_servers/fault_tree_analysis_server/src/fault_tree_analysis_server\nuv venv \nuv sync\nuv run -m src\n```\n\n## Available Tools\n\n- `execute_step` - execute fault tree analysis step",
    "config": {
      "name": "fault-tree-analysis",
      "author": "Huawei Technologies Co., Ltd.",
      "description": "Fault Tree Analysis Server",
      "version": "1.0.6",
      "remote": [
        {
          "name": "fault-tree-analysis-mcp",
          "type": "streamableHttp",
          "url": "http://{{FTA_IP}}:{{FTA_PORT}}/mcp",
          "command": "uvx --index-url https://mirrors.tools.huawei.com/pypi/simple --allow-insecure-host mirrors.tools.huawei.com --allow-insecure-host cmc.cloudartifact.gamma.dragon.tools.huawei.com 'https://cmc.cloudartifact.gamma.dragon.tools.huawei.com/artifactory/product_generic/mcp-server/python/fault_tree_analysis_server/1.0.6/fault_tree_analysis_server_1.0.6_1772423777.tar.gz'",
          "config": {
            "mcpServers": {
              "fault-tree-analysis-mcp": {
                "url": "http://{{FTA_IP}}:{{FTA_PORT}}/mcp",
                "type": "streamableHttp"
              }
            }
          },
          "parameters": {
            "FTA_IP": {
              "type": "string",
              "description": "启动的IP地址",
              "required": true,
              "default": "127.0.0.1"
            },
            "FTA_PORT": {
              "type": "string",
              "description": "启动的端口",
              "required": true,
              "default": 8080
            }
          },
          "headers": null
        }
      ],
      "packageDownloadUrl": "https://cmc.cloudartifact.gamma.dragon.tools.huawei.com/artifactory/product_generic/mcp-server/python/fault_tree_analysis_server/1.0.6/fault_tree_analysis_server_1.0.6_1772423777.tar.gz"
    },
    "isRemote": 0,
    "version": "1.0.6",
    "versionAttr": "formal",
    "cmcUrl": "https://cmc.cloudartifact.gamma.dragon.tools.huawei.com/artifactory/product_generic/mcp-server/python/fault_tree_analysis_server/1.0.6/fault_tree_analysis_server_1.0.6_1772423777.tar.gz",
    "tools": [
      {
        "id": "2031252575634964481",
        "serverId": "2031252533461237761",
        "toolName": "execute_step",
        "description": "execute fault tree analysis step",
        "inputSchema": {
          "type": "object",
          "required": [
            "step_no"
          ],
          "properties": {
            "step_no": {
              "type": "string"
            }
          }
        },
        "createTime": "2026-03-10 14:15:27",
        "createBy": null,
        "updateTime": "2026-03-10 14:15:27",
        "updateBy": null
      }
    ],
    "createBy": "chenman 00809796",
    "createTime": "2026-03-10 14:15:17",
    "updateBy": "lijun 30061744",
    "updateTime": "2026-03-10 14:16:29"
  }
}
```

### 响应字段说明

#### 响应外层字段

| 字段名 | 类型 | 是否必须 | 说明 |
|--------|------|----------|------|
| code | Integer | 是 | 响应状态码,200表示成功 |
| errorCode | String | 否 | 错误码,成功时为null |
| message | String | 是 | 响应消息 |
| data | Object | 是 | MCP详情数据对象 |

#### data 对象字段说明

| 字段名 | 类型 | 是否必须 | 说明 |
|--------|------|----------|------|
| id | String | 是 | 主键ID,自动递增 string类型的long值 |
| mcpId | String | 是 | MCP唯一标识 |
| manifestId | String | 是 | 同发布商内全局唯一标识 |
| publisherId | String | 是 | 发布商ID |
| name | String | 是 | 服务器名称 |
| description | String | 是 | 服务器描述 |
| tagList | Array\<String\> | 是 | 标签列表 |
| categoryList | Array\<String\> | 是 | 分类列表 |
| content | String | 是 | 服务详情内容 |
| config | Object | 是 | 启动server配置 |
| isRemote | Integer | 是 | 是否远程运行(0:本地, 1:远程) |
| version | String | 是 | 版本号 |
| versionAttr | String | 是 | 版本类型(gray:灰度发布版, formal:正式版) |
| cmcUrl | String | 是 | CMC仓库下载地址 |
| tools | Array\<Object\> | 是 | 工具列表 |
| createBy | String | 是 | 创建者 |
| createTime | String | 是 | 创建时间 |
| updateBy | String | 是 | 更新者 |
| updateTime | String | 是 | 更新时间 |

#### config 对象字段说明

| 字段名 | 类型 | 是否必须 | 说明 |
|--------|------|----------|------|
| name | String | 是 | MCP名称 |
| author | String | 是 | 作者名称 |
| description | String | 是 | 描述信息 |
| version | String | 是 | 版本号信息 |
| remote | Array\<Object\> | 是 | 远程server配置列表 |
| packageDownloadUrl | String | 是 | 临时下载链接 |

#### remote 数组对象字段说明

| 字段名 | 类型 | 是否必须 | 说明 |
|--------|------|----------|------|
| name | String | 是 | remote配置名称 |
| type | String | 是 | 类型(如: streamableHttp) |
| url | String | 是 | 服务地址URL |
| command | String | 是 | 启动命令 |
| config | Object | 是 | mcpServers配置 |
| parameters | Object | 是 | 参数配置 |
| headers | Object| 否 | 请求头配置 |

#### parameters 对象字段说明

| 字段名 | 类型 | 是否必须 | 说明 |
|--------|------|----------|------|
| type | String | 是 | 参数类型 |
| description | String | 是 | 参数描述 |
| required | Boolean | 是 | 是否必须 |
| default | Any | 否 | 默认值 |

#### tools 数组对象字段说明

| 字段名 | 类型 | 是否必须 | 说明 |
|--------|------|----------|------|
| id | String | 是 | 工具主键ID |
| serverId | String | 是 | 所属服务器ID |
| toolName | String | 是 | 工具名称 |
| description | String | 是 | 工具描述 |
| inputSchema | Object | 是 | 输入参数Schema |
| createTime | String | 是 | 创建时间 |
| createBy | String/null | 否 | 创建者 |
| updateTime | String | 是 | 更新时间 |
| updateBy | String/null | 否 | 更新者 |

#### inputSchema 对象字段说明

| 字段名 | 类型 | 是否必须 | 说明 |
|--------|------|----------|------|
| type | String | 是 | Schema类型 |
| required | Array\<String\> | 是 | 必填参数列表 |
| properties | Object | 是 | 参数属性定义 |