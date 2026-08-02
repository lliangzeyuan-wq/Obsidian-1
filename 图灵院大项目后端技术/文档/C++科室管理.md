## 问题 1：同一类科室 ID 在不同接口中类型不一致

### 当前文档

详情、删除、新增和修改接口中的 ID 使用字符串：

```json
{
  "id": "322663218269917184",
  "parentId": "322663105141149696",
  "defDoctorId": "308791351298756608"
}
```

但批量启停接口中的 `deptIdList` 被定义为 `integer(int64)[]`：

```json
{
  "deptIdList": [322663218269917184],
  "targetStatus": 1
}
```

### 存在的问题

科室 ID 可能达到 18～19 位，超过 JavaScript 能安全表示的整数范围。批量接口如果使用 JSON 数字，浏览器可能在没有报错的情况下改变 ID，导致启停失败或操作错误科室。

### 修改要求

所有科室及人员相关 ID 应统一使用字符串，包括：

- `id: string`；
- `parentId: string | null`；
- `defDoctorId: string | null`；
- `deptIdList: string[]`。

建议批量接口修改为：

```json
{
  "deptIdList": ["322663218269917184"],
  "targetStatus": 1
}
```

### 

> 请确认 C++ 真实代码中所有 ID 的最终 JSON 类型，并将批量启停的 deptIdList 从 int64[] 改为 string[]。需要确认的是网络 JSON 类型，不只是数据库或 C++ 变量类型。

## 问题 2：获取科室详情缺少编辑页面必需字段

接口：`GET /organization/detail?id=...`

### 当前文档

`OrganizationDetailDTO` 返回：

```text
id、name、type_enum、class_enum、status_enum、yb_no、yb_name、
medins_id、medins_admdvs、medins_type、medins_lv、py_str、
wb_str、display_order
```

但是没有返回：

- `parentId`：原上级医院或科室；
- `defDoctorId`：原默认医师；
- `busNo`：科室业务编码；
- `caty`：科别。

### 存在的问题

新增和修改接口可以提交 `parentId`、`defDoctorId` 和 `caty`，说明这些字段属于科室数据。但详情接口不返回它们，用户打开编辑弹窗后，前端无法回填原上级科室和默认医师。

如果前端提交一个没有完整回填的表单，可能把原数据清空或错误覆盖。

### 修改要求

详情接口至少补充：

```json
{
  "id": "322663218269917184",
  "name": "门诊内科",
  "parentId": "322663105141149696",
  "defDoctorId": "308791351298756608",
  "busNo": "OR0007.OR0008",
  "caty": "内科"
}
```

如果响应继续使用下划线命名，也应补充对应的 `parent_id`、`def_doctor_id`、`bus_no`。

### 

> 请补充详情接口用于编辑回填的完整字段，重点是 parentId 和 defDoctorId。请同时确认 busNo 和 caty 是否需要在详情中返回。

## 问题 3：科室列表树缺少当前页面需要展示的字段

接口：`GET /organization/tree`

### 当前文档

列表树节点只有：

```text
id、name、type_enum、class_enum、status_enum、children
```

### 存在的问题

科室管理页面需要显示科室编码、类型、类别、排序和状态。当前响应缺少：

- `busNo`：科室编码；
- `displayOrder`：显示顺序；
- 类型、类别和状态的中文文本，或者至少提供明确的枚举映射。

缺少这些字段时，页面中的“科室编码”和“排序”列没有数据，类型、类别和状态也无法可靠地显示中文。

### 修改要求

如果 `/organization/tree` 是科室管理主页面接口，建议节点至少返回：

```json
{
  "id": "322663218269917184",
  "name": "门诊内科",
  "busNo": "OR0007.OR0008",
  "typeEnum": "明确的类型值",
  "typeEnumDictText": "科室",
  "classEnum": "明确的类别值",
  "classEnumDictText": "门诊",
  "statusEnum": "明确的状态值",
  "statusEnumDictText": "已启用",
  "displayOrder": 1,
  "children": []
}
```

### 

> `/organization/tree` 是否就是科室管理主页面使用的完整列表树？如果是，请补充 busNo、displayOrder 和必要的中文显示字段；如果不是，请提供真正用于管理页面的列表接口。

## 问题 4：请求与响应的字段命名规则不一致

### 当前文档

新增和修改请求使用驼峰命名：

```text
typeEnum、classEnum、parentId、defDoctorId、displayOrder
```

详情和树响应使用下划线命名：

```text
type_enum、class_enum、status_enum、display_order
```

### 存在的问题

同一个业务字段在请求和响应中使用两套名称，前端必须额外做转换，容易出现遗漏。例如页面读取 `typeEnum` 时，接口实际只返回 `type_enum`，字段会变成空值。

### 修改要求

建议请求和响应统一使用驼峰命名：

```text
typeEnum、classEnum、statusEnum、parentId、defDoctorId、displayOrder
```

如果 C++ 组必须使用下划线，也需要保证所有请求和响应统一，并在文档中明确说明。不要请求用驼峰、响应用下划线。

### 

> 请确定科室接口唯一的字段命名规范，并统一新增、修改、详情、名称树和列表树。前端建议统一使用驼峰字段。

## 问题 5：科室类型、类别和状态没有枚举值说明

### 当前文档

文档只写了：

```text
typeEnum: string
classEnum: string
statusEnum: string
```

查询参数也只写成普通字符串，没有枚举、中文说明或示例。

### 存在的问题

前端无法判断应当提交：

- `HOSPITAL/DEPARTMENT`，还是 `1/2`；
- `CLINIC/INPATIENT`，还是数字代码；
- `ACTIVE/INACTIVE`，还是 `1/0`。

不同后端组如果使用不同枚举，页面不能直接复用同一套数据。

### 修改要求

请提供完整枚举表，至少包括：

| 字段 | 需要说明的内容 |
|---|---|
| `typeEnum` | 所有值、中文名称、JSON 类型 |
| `classEnum` | 所有值、中文名称、JSON 类型 |
| `statusEnum` | 所有值、中文名称、JSON 类型 |
| `targetStatus` | 与 `statusEnum` 的对应关系 |

同时把枚举约束直接配置到 Apifox Schema 中，而不是只在聊天中说明。

### 

> 请提供 typeEnum、classEnum、statusEnum 的完整枚举表，并明确请求和响应使用相同值还是需要转换。

## 问题 6：`displayOrder` 的类型前后不一致

### 当前文档

新增和修改请求中：

```text
displayOrder: string
```

详情响应中：

```text
display_order: integer
```

### 存在的问题

显示顺序本身是数字，同一个字段请求用字符串、响应用整数，会增加无意义的类型转换，也可能导致后端排序按字符串处理。例如字符串排序时 `"10"` 可能排在 `"2"` 前面。

### 修改要求

请求和响应统一为整数：

```text
displayOrder: integer(int32)
```

并说明：

- 是否允许负数；
- 不传时的默认值；
- 数值相同时如何排序。

### 

> 请将新增、修改和查询中的 displayOrder 统一为 integer，并补充默认值和允许范围。

## 问题 7：`statusEnum`、`activeFlag`、`targetStatus` 三套状态字段关系不清楚

### 当前文档

新增和修改 DTO 同时包含：

```text
statusEnum
activeFlag
```

批量启停接口又使用：

```text
targetStatus：0 停用、1 启用
```

### 存在的问题

目前无法判断：

- `statusEnum` 与 `activeFlag` 是否表示同一件事；
- 新增时应该传哪个；
- 修改普通资料时是否允许改变状态；
- 两个字段值冲突时以谁为准；
- `targetStatus` 与 `statusEnum` 如何对应。

### 修改要求

请指定一个唯一业务状态来源。建议：

- 查询和返回使用 `statusEnum`；
- 批量启停使用 `targetStatus`，并明确与 `statusEnum` 的映射；
- 如果 `activeFlag` 是旧字段或数据库字段，从对外 DTO 删除；
- 普通修改接口是否允许修改状态，需要明确规定。

### 

> 请明确 statusEnum、activeFlag、targetStatus 的关系，以及新增、普通修改、批量启停分别允许传哪些状态字段。

## 问题 8：新增科室的必填和条件校验规则不完整

接口：`POST /department/createDepartment`

### 当前文档

`DeptAddDTO` 只将 `name` 标记为必填，`typeEnum`、`classEnum`、`parentId` 等全部是可选字段。

### 存在的问题

文档没有说明：

- `typeEnum` 是否必填；
- 创建普通科室时 `parentId` 是否必填；
- 创建医院时 `parentId` 是否必须为空；
- 普通科室的 `classEnum` 是否必填；
- `name` 的长度和重复规则；
- `defDoctorId` 是否校验医生存在；
- `displayOrder` 和状态不传时使用什么默认值。

### 修改要求

将实际代码中的校验规则同步到 Apifox，包括必填、长度、枚举、条件必填和错误响应。

### 

> 请分别提供“新增根医院”和“新增普通科室”的完整请求示例，并明确每个字段的必填及条件校验规则。

## 问题 9：修改科室的业务规则没有说明

接口：`POST /department/editDepartment`

### 当前文档

修改 DTO 只明确 `id` 和 `name` 必填，没有说明其他字段是“未传则保持原值”，还是“未传则清空”。

### 存在的问题

如果前端没有传 `parentId`、`defDoctorId` 等字段，后端行为不明确，可能误清空原数据。另外也没有说明修改父级时是否校验循环关系。

### 修改要求

请明确：

1. 这是全量修改还是部分修改；
2. 字段不传、传 `null`、传空字符串分别代表什么；
3. 修改 `parentId` 时是否禁止选择自己或自己的子孙节点；
4. 医院和普通科室互相修改类型时如何处理；
5. 修改 `defDoctorId` 时是否校验医生存在。

### 

> 请明确 editDepartment 的全量/部分更新规则，以及空值、父级变更、循环关系和默认医师的校验方式。

## 问题 10：批量启停字段没有标记必填，取值约束也不完整

接口：`POST /dept/batch/status`

### 当前文档

`deptIdList` 和 `targetStatus` 都没有标记为必填。`targetStatus` 虽然说明为 `0/1`，Schema 允许的却是整个 int32 范围。

按照当前文档，下面的请求也可能通过文档校验：

```json
{}
```

### 存在的问题

批量启停至少需要有效 ID 列表和目标状态。还没有说明父子科室之间的启停限制及批量失败规则。

### 修改要求

- `deptIdList` 必填且至少包含一个 ID；
- ID 类型改为字符串；
- `targetStatus` 必填；
- `targetStatus` 只允许 `0` 或 `1`；
- 说明重复 ID、无效 ID 和空数组如何处理；
- 说明停用父科室、启用子科室时的父子状态规则；
- 说明某一项失败时整批回滚还是部分成功。

### 

> 请补全批量启停的必填、枚举、父子状态、事务和失败响应规则。

## 问题 11：科室名称树没有说明如何排除无效上级节点

接口：`GET /organization/name-tree`

### 当前文档

名称树只支持：

```text
type_enum、class_enum
```

没有状态筛选和排除当前节点的参数。

### 存在的问题

名称树用于选择上级科室时，需要避免：

- 选择已停用科室作为上级；
- 选择当前科室自己；
- 选择当前科室的子孙节点，造成循环关系。

### 修改要求

请说明后端是否自动过滤停用节点，以及是否支持类似：

```text
status_enum
exclude_id
```

其中 `exclude_id` 用于编辑时排除当前科室及其子孙节点。

### 

> name-tree 用于选择上级科室时，如何排除停用科室、当前科室和当前科室的子孙节点？请补充参数或明确后端过滤规则。

## 问题 12：列表树的关键字搜索规则没有说明

接口：`GET /organization/tree`

### 当前文档

接口支持 `keyword`，但没有说明命中子科室后如何返回父子层级。

### 存在的问题

搜索“心血管内科”时，前端需要知道接口返回：

- 只返回“心血管内科”；
- 返回“医院—内科—心血管内科”的完整祖先路径；
- 是否同时返回它的子节点；
- 未命中时返回空数组还是 `null`。

### 修改要求

补充搜索行为说明，并提供至少一份命中三级树子节点的真实响应。

### 

> 请明确 organization/tree 的 keyword 搜索规则，特别是是否保留祖先路径和是否返回匹配节点的子节点。

## 问题 13：删除科室的限制和失败响应没有说明

接口：`POST /department/deleteDepartment`

### 当前文档

删除请求只包含 `id`，没有业务规则和失败响应。

### 存在的问题

至少需要明确：

- 有子科室时能否删除；
- 已被用户、医生或其他业务引用时能否删除；
- 启用状态下是否允许删除；
- 是物理删除还是逻辑删除；
- 科室不存在或无权限时返回什么错误。

### 修改要求

补充删除前置条件、错误码和失败响应示例。

### 

> 请提供 deleteDepartment 的子科室、业务引用、启用状态、逻辑删除及失败返回规则。

## 问题 14：新增、修改和删除的成功返回 `data` 含义不清楚

### 当前文档

新增、修改和删除响应中的 `data` 都只是普通 `string`，说明统一写成“数据对象”。

### 存在的问题

前端无法判断：

- 新增成功是否返回新科室 ID；
- 修改成功是否返回 ID 或提示文字；
- 删除成功是否返回被删除 ID；
- 没有数据时返回 `null`、空字符串还是不返回 `data`。

### 修改要求

为每个接口明确 `data` 的准确含义和示例。建议新增成功返回字符串 ID，其他无业务数据的操作统一返回 `null` 或固定结构。

### 

> 请分别明确 createDepartment、editDepartment、deleteDepartment 成功响应中 data 的正式类型、含义和真实示例。

## 问题 15：所有接口只有 200，没有失败响应模型

### 当前文档

7 个接口都只配置了 `200 OK`，没有参数错误、数据不存在、无权限、冲突或服务器错误的响应。

### 存在的问题

前端无法根据错误码区分：

- 参数填写错误；
- 科室不存在；
- 科室名称重复；
- 存在子科室不能删除；
- 父子状态不允许启停；
- 没有操作权限；
- 服务器异常。

### 修改要求

统一错误响应结构，并为常见业务失败配置真实示例。至少说明 HTTP 状态码、业务 `code`、`message` 和 `data` 的含义。

### 

> 请补充所有接口的常见失败响应和业务错误码，不要只配置 200 成功响应。

## 问题 16：接口路径前缀和资源名称不统一

### 当前文档

同一个科室管理模块使用了三套路径：

```text
/department/createDepartment
/dept/batch/status
/organization/tree
```

新增、修改、删除也全部使用 `POST`。

### 存在的问题

这不一定导致接口不能运行，但接口命名缺乏统一规则，容易让前端误认为 `/department`、`/dept` 和 `/organization` 是不同资源，也增加网关配置和后期维护成本。

### 修改要求

建议统一资源前缀。



