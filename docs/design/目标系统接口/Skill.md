**该文档提供的接口均为非公开接口，需授权后才能使用，授权请联系宋启旺 00889257 和 杨鹏 00454339**

## 接口鉴权

apig鉴权见：[apig请求方法](https://his-beta.huawei.com/doc/#/page.html?service_code=hrn:hic:servicemarket::service:apigw&group_id=bce14fb2eda34368917bf22dddaaf78b&lang=zh_CN)

接口异常响应示例：[API网关错误返回说明](https://his-beta.huawei.com/doc/#/page.html?service_code=hrn:hic:servicemarket::service:apigw&group_id=28a3c933a23a4054b054035824866835&lang=zh_CN)

请求头携带X-HW-ID、X-HW-APPKEY和云龙token参数

| header key | value |
|--------|------|
| X-AUTH-TOKEN | 云龙token  |
| X-HW-ID | API所授权凭据的APPID  |
| X-HW-APPKEY | API所授权凭据的APPKEY  |

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

## Skill列表查询

分页模糊查询skill列表

### 请求地址

请求方法 POST

| 环境 | 服务地址 |
|--------|------|
| **alpha apig** | https://apigw-beta.huawei.com/api/alpha/mcpService/external/skills/v1/query |
| **beta apig** | https://apigw-beta.huawei.com/api/mcpService/external/skills/v1/query |
| **prod apig** | https://apigw-cn-south02.huawei.com/api/mcpService/external/skills/v1/query |

### 请求参数

ContentType: application/json

| 参数名 | 类型 | 是否必填 |示例值 | 描述 |
|--------|------|--------|------|------|
| **keyword** | string | 否 | code | 匹配skill名称或描述的模糊搜索关键字 |
| **pageSize** | int | 否 | 10 | 请求分页大小，默认值10，最大值1000 |
| **pageNum** | int | 否 | 1 | 当前分页，默认值1 |
| **publishLevel** | String | 否 | ORGANIZATION | 发布级别，枚举（COMPANY:公司级 ORGANIZATION:组织级 PERSONAL 个人级） |
| **sortField** | 枚举（DOWNLOAD_COUNT:下载量,ACCESS_COUNT:访问量,CREATE_TIME:上传时间）| 否 | CREATE_TIME| 查询排序字段|
| **sortOrder** | 枚举（ASC:升序,DESC:降序）| 否 | DESC| 排序|
| **publisherId** | long | 否 | 1| 发布商id|

请求参数示例

```json
{
    "keyword": "code",
    "pageNum": 1,
    "pageSize": 10
}
```

### 接口响应示例

```json
{
    "code": 200,
    "errorCode": null,
    "message": "OK",
    "data": {
        "pageNum": 1,
        "pageSize": 20,
        "total": 3,
        "data": [
            {
                "id": "603",
                "skillId": "agents-md-merger",
                "name": "agents-md-merger",
                "tags": [
                    {
                        "tagId": "1",
                        "name": "研发工具生产线"
                    },
                    {
                        "tagId": "100",
                        "name": "CodeAgent"
                    }
                ],
                "latestDescription": "融合多层级 AGENTS.md、CODEAGENT.md 和 CLAUDE.md 文件，支持渐进式披露。当用户请求合并/融合 Agent 配置文件或整合子目录的 AGENTS.md 时调用。",
                "latestVersion": "0.0.3",
                "createBy": "yangpeng 00454339",
                "createTime": "2026-03-17 20:57:40",
                "updateBy": "yangpeng 00454339",
                "updateTime": "2026-03-17 21:00:33",
                "downloadCount": 4,
                "accessCount": 0,
                "publisherId": "13",
                "publisher": "cTest1",
                "publishLevel": "ORGANIZATION"
            },
            {
                "id": "599",
                "skillId": "pdf-test-13-p",
                "name": "pdf-test-13-p",
                "tags": [
                    {
                        "tagId": "1",
                        "name": "研发工具生产线"
                    },
                    {
                        "tagId": "26",
                        "name": "mcp"
                    }
                ],
                "latestDescription": "Comprehensive PDF manipulation toolkit for extracting text and tables, creating new PDFs, merging/splitting documents, and handling forms. When Claude needs to fill in a PDF form or programmatically process, generate, or analyze PDF documents at scale.",
                "latestVersion": "0.0.9",
                "createBy": "yuanjian 00887662",
                "createTime": "2026-03-03 17:13:11",
                "updateBy": "yuanjian 00887662",
                "updateTime": "2026-03-11 16:16:21",
                "downloadCount": 25,
                "accessCount": 0,
                "publisherId": "13",
                "publisher": "cTest1",
                "publishLevel": "ORGANIZATION"
            },
            {
                "id": "412",
                "skillId": "skill-pdf",
                "name": "skill-pdf",
                "tags": [
                    {
                        "tagId": "1",
                        "name": "研发工具生产线"
                    }
                ],
                "latestDescription": "Comprehensive PDF manipulation toolkit for extracting text and tables, creating new PDFs, merging/splitting documents, and handling forms. When Claude needs to fill in a PDF form or programmatically process, generate, or analyze PDF documents at scale.",
                "latestVersion": "0.0.2",
                "createBy": "liujiangfeng 30030053",
                "createTime": "2026-02-25 19:49:53",
                "updateBy": "chengyufan 30076406",
                "updateTime": "2026-03-19 19:01:02",
                "downloadCount": 12,
                "accessCount": 0,
                "publisherId": "13",
                "publisher": "cTest1",
                "publishLevel": "ORGANIZATION"
            }
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
| data.data     | array[SkillVO]   | 是       | 数据列表，包含多个数据对象。 |

SkillVO响应字段说明

| 字段名称        | 类型    | 是否必须 | 描述                                     |
| ----------------- | --------- | ---------- | ------------------------------------------ |
| id              | string | 是       | 主键id, string格式long值                         |
| skillId            | string  | 是       |  skill标识，与skill上传时的目录名一致    |
| name            | string  | 是       |  skill名称，与上传时md文件中的name一致    |
| tags            | array[TagVO] | 是       |  标签列表    |
| latestDescription            | string  | 是       | 最新版本的描述  |
| latestVersion       | string  | 否       | 最新版本号  |
| createBy     | string  | 是       | 创建者 |
| createTime         | string  | 是       | 创建时间，格式为 "yyyy-MM-dd HH:mm:ss" |
| updateBy   | string  | 是       | 更新者  |
| updateTime         | string   | 是       | 更新时间，格式为 "yyyy-MM-dd HH:mm:ss"    |
| downloadCount    | int   | 是       | 下载次数     |
| accessCount         | int   | 是       | 访问次数                      |
| publisherId         | String   | 是       | 发布商id,long类型字符串                     |
| publisher         | String   | 是       | 发布商                      |
| publishLevel         | String   | 是       | 发布级别                      |

TagVO响应字段说明

| 字段名称 | 类型    | 是否必须 | 描述                                       |
| ---------- | --------- | ---------- | -------------------------------------------- |
| tagId | string | 是       | 标签id |
| name | string | 是       | 标签名称 |

## Skill详情查询

通过详情接口，获取skilll详细信息

### 请求地址

请求方法 GET

| 环境 | 地址 |
|--------|------|
| **alpha apig** | https://apigw-beta.huawei.com/api/mcpService/alpha/external/skills/v1/get 
| **beta apig** | https://apigw-beta.huawei.com/api/mcpService/external/skills/v1/get |
| **prod apig** | https://apigw-cn-south02.huawei.com/api/mcpService/external/skills/v1/get |

### 请求参数

path参数

| 参数名 | 类型 | 是否必填 |示例值 | 描述 |
|--------|------|--------|-------|------|
| **skillId** | string | 是 |frontend-design | skill标识，与skill上传时的目录名一致 |
| **version** | string | 否 | 0.0.1 | skill版本号，为空时查询最新版本 |

### 接口响应示例

```json
{
    "code": 200,
    "errorCode": null,
    "message": "OK",
    "data": {
        "id": "2005936474419109889",
        "skillId": "frontend-design",
        "name": "frontend-design",
        "version": "0.0.1",
        "content": "This skill guides creation of distinctive, production-grade frontend interfaces that avoid generic \"AI slop\" aesthetics. Implement real working code with exceptional attention to aesthetic details and creative choices.\n\nThe user provides frontend requirements: a component, page, application, or interface to build. They may include context about the purpose, audience, or technical constraints.\n\n## Design Thinking\n\nBefore coding, understand the context and commit to a BOLD aesthetic direction:\n- **Purpose**: What problem does this interface solve? Who uses it?\n- **Tone**: Pick an extreme: brutally minimal, maximalist chaos, retro-futuristic, organic/natural, luxury/refined, playful/toy-like, editorial/magazine, brutalist/raw, art deco/geometric, soft/pastel, industrial/utilitarian, etc. There are so many flavors to choose from. Use these for inspiration but design one that is true to the aesthetic direction.\n- **Constraints**: Technical requirements (framework, performance, accessibility).\n- **Differentiation**: What makes this UNFORGETTABLE? What's the one thing someone will remember?\n\n**CRITICAL**: Choose a clear conceptual direction and execute it with precision. Bold maximalism and refined minimalism both work - the key is intentionality, not intensity.\n\nThen implement working code (HTML/CSS/JS, React, Vue, etc.) that is:\n- Production-grade and functional\n- Visually striking and memorable\n- Cohesive with a clear aesthetic point-of-view\n- Meticulously refined in every detail\n\n## Frontend Aesthetics Guidelines\n\nFocus on:\n- **Typography**: Choose fonts that are beautiful, unique, and interesting. Avoid generic fonts like Arial and Inter; opt instead for distinctive choices that elevate the frontend's aesthetics; unexpected, characterful font choices. Pair a distinctive display font with a refined body font.\n- **Color & Theme**: Commit to a cohesive aesthetic. Use CSS variables for consistency. Dominant colors with sharp accents outperform timid, evenly-distributed palettes.\n- **Motion**: Use animations for effects and micro-interactions. Prioritize CSS-only solutions for HTML. Use Motion library for React when available. Focus on high-impact moments: one well-orchestrated page load with staggered reveals (animation-delay) creates more delight than scattered micro-interactions. Use scroll-triggering and hover states that surprise.\n- **Spatial Composition**: Unexpected layouts. Asymmetry. Overlap. Diagonal flow. Grid-breaking elements. Generous negative space OR controlled density.\n- **Backgrounds & Visual Details**: Create atmosphere and depth rather than defaulting to solid colors. Add contextual effects and textures that match the overall aesthetic. Apply creative forms like gradient meshes, noise textures, geometric patterns, layered transparencies, dramatic shadows, decorative borders, custom cursors, and grain overlays.\n\nNEVER use generic AI-generated aesthetics like overused font families (Inter, Roboto, Arial, system fonts), cliched color schemes (particularly purple gradients on white backgrounds), predictable layouts and component patterns, and cookie-cutter design that lacks context-specific character.\n\nInterpret creatively and make unexpected choices that feel genuinely designed for the context. No design should be the same. Vary between light and dark themes, different fonts, different aesthetics. NEVER converge on common choices (Space Grotesk, for example) across generations.\n\n**IMPORTANT**: Match implementation complexity to the aesthetic vision. Maximalist designs need elaborate code with extensive animations and effects. Minimalist or refined designs need restraint, precision, and careful attention to spacing, typography, and subtle details. Elegance comes from executing the vision well.\n\nRemember: Claude is capable of extraordinary creative work. Don't hold back, show what can truly be created when thinking outside the box and committing fully to a distinctive vision.\n",
        "description": "Create distinctive, production-grade frontend interfaces with high design quality. Use this skill when the user asks to build web components, pages, artifacts, posters, or applications (examples include websites, landing pages, dashboards, React components, HTML/CSS layouts, or when styling/beautifying any web UI). Generates creative, polished code and UI design that avoids generic AI aesthetics.",
        "archiveUrl": "https://s3-kp-kwe.his-beta.huawei.com/mcp-market-bucket-alpha/package/231cbcae5ae0421ca0b204aaf92883b0.zip",
        "createBy": "z30031055",
        "createTime": "2025-12-30 17:38:18",
        "updateBy": "z30031055",
        "updateTime": "2025-12-30 17:38:18",
        "downloadCount": 18,
        "accessCount": 0
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
| **id**              | string | 是       | 主键id, string格式long值                         |
| **skillId**            | string  | 是       |  skill标识，与skill上传时的目录名一致    |
| **name**            | string  | 是       |  skill名称，与上传时md文件中的name一致    |
| **version** | string |是|  版本号 |
| **content** | string| 是 | 全量文本 |
| **description** | string | 否 | skill描述 |
| **archiveUrl** | string | 否 | s3归档地址 |
| **createBy**     | string  | 是       | 创建者 |
| **createTime**         | string  | 是       | 创建时间，格式为 "yyyy-MM-dd HH:mm:ss" |
| **updateBy**   | string  | 是       | 更新者  |
| **updateTime**         | string   | 是       | 更新时间，格式为 "yyyy-MM-dd HH:mm:ss"    |
| **downloadCount**    | int   | 是       | 下载次数     |
| **accessCount**         | int   | 是       | 访问次数                      |

## Skill上传

通过上传接口将本地开发的skill工具上传至远端市场

### 请求地址

请求方法 POST

| 环境 | 地址 |
|--------|------|
| **alpha** | https://agent.alpha.his-beta.huawei.com/mcpService/external/skills/v1/upload |
| **beta** | https://agent.beta.his-beta.huawei.com/mcpService/external/skills/v1/upload |

### 请求参数

ContentType: form-data

| 参数名 | 类型 | 是否必填 |示例值 | 描述 |
|--------|------|--------|------|------|
| **file** | file | 是 | - | Skill ZIP压缩包，Skill开发规范参考：[skill使用指导](https://wiki.huawei.com/domains/162748/wiki/8/WIKI202601229895654) |
| **param** | object | 是 | - | skill基本信息 |

param参数说明

| 参数名 | 类型 | 是否必填 |示例值 | 描述 |
|--------|------|--------|------|------|
| **skillId** | string | 是 | frontend-design | skill标识，要求与skill目录名一致 |
| **name** | string | 是 |frontend-design | skill名称，要求与md文件中的name |
| **description** | string | 是 | Create distinctive, production-grade frontend interfaces with high design quality. | 说明描述 |
| **version** | string | 否 | 0.0.1 | 版本号，格式为x.x.x。为空时，根据最新版本号自增 |
| **content** | string | 否 | - | md文件全量文本 |

param示例

```json
{
    "skillId": "frontend-design",
    "version": "0.0.1",
    "description": "Create distinctive, production-grade frontend interfaces with high design quality. Use this skill when the user asks to build web components, pages, artifacts, posters, or applications (examples include websites, landing pages, dashboards, React components, HTML/CSS layouts, or when styling/beautifying any web UI). Generates creative, polished code and UI design that avoids generic AI aesthetics.",
    "name": "frontend-design",
    "content": ""
 }
```

### 约束

1.已完成上传的 Skill，其他发布者不得再上传同名的 Skill

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

## Skill下载（大流量禁止使用该接口）

通过下载接口将远端市场的skill工具下载至本地

### 请求地址

请求方法 POST

| 环境 | 地址 |
|--------|------|
| **alpha apig** | https://apigw-beta.huawei.com/api/mcpService/alpha/external/skills/v1/download |
| **beta apig** | https://apigw-beta.huawei.com/api/mcpService/external/skills/v1/download |
| **prod apig** | https://apigw-cn-south02.huawei.com/api/mcpService/external/skills/v1/download |

### 请求参数

ContentType: application/json

| 参数名 | 类型 | 是否必填 |示例值 | 描述 |
|--------|------|--------|------|------|
| **skillId** | string | 是 | frontend-design | skill标识 |
| **version** | string | 否 | 0.0.1 | 版本号，格式x.x.x，当版本号为空时，下载最新版本的skill |

请求参数示例

```json
{
    "skillId": "frontend-design",
    "version": "0.0.1"
 }
```

### 响应字段说明

返回文件流
Content-Type：application/octet-stream

## Skill删除

删除远程市场的Skill工具

### 请求地址

请求方法 DELETE

| 环境 | 地址 |
|--------|------|
| **alpha** | https://agent.alpha.his-beta.huawei.com/mcpService/external/skills/v1/delete |
| **beta** | https://agent.beta.his-beta.huawei.com/mcpService/external/skills/v1/delete |

### 请求参数

ContentType: application/json

| 参数名 | 类型 | 是否必填 |示例值 | 描述 |
|--------|------|--------|------|------|
| **skillId** | string | 是 | frontend-design | skill标识 |
| **version** | string | 否 | 0.0.1 | 版本号，格式x.x.x，当版本号为空时，删除最新版本的skill |

请求参数示例

```json
{
    "skillId": "frontend-design",
    "version": "0.0.1"
 }
```

### 约束

1.仅发布者本身可删除自己上传的Skill
2.version为空时，删除最新版本

### 响应字段说明

| 字段名 | 类型 | 描述 |
|--------|------|------|
| **code** | integer | 响应状态码，200表示成功。 |
| **message** | string | 响应消息，通常为"OK"。 |
| **data** | boolean | true:成功，false:失败 |

## Skill查询临时下载链接（大流量使用该接口）

Skill获取临时下载链接

### 请求地址

请求方法 GET

| 环境 | 地址 |
|--------|------|
| **alpha** | https://apigw-beta.huawei.com/api/alpha/mcpService/external/skills/link/v1/get |
| **beta-apig** | https://apigw-beta.huawei.com/api/mcpService/external/skills/link/v1/get |
| **prod-apig** | https://apigw-cn-south02.huawei.com/api/mcpService/external/skills/link/v1/get |

### 请求参数

path参数

| 参数名 | 类型 | 是否必填 |示例值 | 描述 |
|--------|------|--------|-------|------|
| **skillId** | string | 是 |frontend-design | skill标识，与skill上传时的目录名一致 |
| **version** | string | 是 | 0.0.1 | skill版本号 |

### 接口响应示例

```json
{
    "code": 200,
    "errorCode": null,
    "message": "OK",
    "data": {
        "downloadUrl": "https://s3-kp-kwe.his-beta.huawei.com:443/mcp-market-bucket-alpha/package/882c455ae7074c6c990f57929825bd7a.zip"
    }
}
```

### 响应字段说明

| 字段名 | 类型 | 描述 |
|--------|------|------|
| **code** | integer | 响应状态码，200表示成功。 |
| **message** | string | 响应消息，通常为"OK"。 |
| **data** | boolean | true:成功，false:失败 |
| **data.downloadUrl** | string| 临时下载链接 |