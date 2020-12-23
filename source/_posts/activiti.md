---
title: activiti笔记
date: 2019-06-17 00:22:05
categories: 
- java
---

activiti框架学习笔记

<!-- more -->

# 数据库设计

## 通用数据表（以ACT_GE_开头）

存放一些通用数据

### 资源表（ACT_GE_BYTEARRAY）

用于保存于流程引擎相关的资源，资源会被转换为byte数组保存到这个表中。一般情况下，activiti使用这个表来保存字符串、流程文件的内容和流程图片内容

| 字段           | 含义                         | 关联表               |
| -------------- | ---------------------------- | -------------------- |
| ID_            | 主键                         |                      |
| REV_           | 数据版本                     |                      |
| NAME_          | 资源名称                     |                      |
| DEPLOYMENT_ID_ | 部署ID                       | ACT_RE_DEPLOYMENT_ID |
| BYTES_         | 资源内容                     |                      |
| GENERATED_     | 是否有activiti自动产生的资源 |                      |

### 属性表（ACT_GE_PROPERTY）

| 字段   | 含义       | 关联表 |
| ------ | ---------- | ------ |
| NAME_  | 属性名称   |        |
| VALUE_ | 属性值     |        |
| REV_   | 数据版本号 |        |

> 初始化时会自动加入4个属性：
>
> historyLevel:
>
> next.dbid:
>
> schema.history:
>
> schema.versino:

## 流程存储表（以ACT_RE_开头）

保存流程定义和部署信息

### 部署数据表（ACT_RE_DEPLOYMENT）

对流程部署这一操作的记录

| 字段             | 含义     | 关联表 |
| ---------------- | -------- | ------ |
| ID_              | 主键     |        |
| NAME_            | 部署名称 |        |
| DEPLOYMENT_TIME_ | 部署时间 |        |

### 流程定义表（ACT_RE_PROCDEF）

| 字段                | 说明                                                         | 关联表            |
| ------------------- | ------------------------------------------------------------ | ----------------- |
| ID_                 | 主键                                                         |                   |
| REV_                | 数据版本                                                     |                   |
| CATEGORY_           | 流程定义分类                                                 |                   |
| NAME_               | 流程定义的名称，bpmn的xml文件中process元素的name             |                   |
| KEY_                | 流程定义的key，bpmn的xml文件中process元素的id                |                   |
| VERSION             | 流程定义的版本，流水号，同名流程文件重复部署，版本号+1       |                   |
| DEPLOYMENT_ID       | 流程定义的对应的部署数ID                                     | ACT_RE_DEPLOYMENT |
| RESOURCE_NAME       | 流程定义对应资源名称，一般问流程文件相对路径                 |                   |
| DGRM_RESOURCE_NAME  | l流程定义对应的流程图的资源名称                              |                   |
| HAS_START_FROM_KEY_ | 流程文件是否有开始表单                                       |                   |
| SUSPENSION_STATE_   | 流程定义的状态是激活还是终止，激活时为1，终止时为2。如果流程定义终止，将不能启动流程 |                   |

## 运行时数据表（以ACT_RU开头）

### 流程实例（执行流）表（ACT_RU_EXECUTION）

流程启动后会产生一个流程实例，同时会产生相应的执行流，如果一个流程实例只有一条执行流，那么该表中只产生一条数据，既表示流程实例也表示执行流

| 字段             | 说明                                               | 关联表           |
| ---------------- | -------------------------------------------------- | ---------------- |
| ID_              | 主键                                               |                  |
| REV_             | 数据版本                                           |                  |
| PROC_INST_ID     | 流程实例id                                         |                  |
| BUSINESS_KEY_    | 业务主键                                           |                  |
| PARENT_ID        | 流程实例的id。表示当前执行流数据属于哪一个流程实例 | ACT_RU_EXECUTION |
| PROC_DEF_ID      | 流程定义id                                         | ACT_RE_PROCDEF   |
| SUPER_EXEC       | 父执行流id。表示当前流程实例属于哪一个执行流       | ACT_RU_EXECUTION |
| ACT_ID           | 当前执行流行为的id                                 |                  |
| IS_ACTIVE        | 当前执行流是否活跃                                 |                  |
| IS_CONCURRENT    | 执行流是否正在并行                                 |                  |
| IS_SCOPE         | 是否在执行流范围内                                 |                  |
| IS_ENEVT_SCOPE   | 是否在事件范围内                                   |                  |
| SUSPENSION_STATE | 标识流程的中断状态                                 |                  |
| CACHED_ENT_STATE | 流程实例的缓存                                     |                  |

### 流程任务表（ACT_RU_TASK）

| 字段           | 说明                                | 关联表           |
| -------------- | ----------------------------------- | ---------------- |
| ID_            | 主键                                |                  |
| REV_           | 数据版本                            |                  |
| ECECUTION_ID   | 任务所处执行流id                    | ACT_RU_EXECUTION |
| PROC_INST_ID   | 对应流程实例id                      | ACT_RU_EXECUTION |
| PROC_DEF_ID    | 对应流程定义id                      | ACT_RE_PROCDEF   |
| NAME_          | 任务名称                            |                  |
| PARENT_TASK_ID | 父任务id                            | ACT_RU_TASK      |
| DESCRIPTINO    | 任务描述                            |                  |
| TASK_DEF_KEY   | 任务定义id，bpmn的xml文件中任务的id |                  |
| OWNER          | 任务拥有人                          |                  |
| ASSIGNEE       | 任务指派人                          |                  |
| DELEGATINO     | 任务委托状态（等待和已解决）        |                  |
| PRIORITY       | 任务优先级                          |                  |
| CREATE_TIME    | 任务创建时间                        |                  |
| DUE_DATE       | 任务预定日期                        |                  |

### 流程参数表（ACT_RU_VARIABLE）

流程参数类型:

* 流程实例参数
* 执行流参数
* 任务参数

| 字段         | 说明                         | 关联表           |
| ------------ | ---------------------------- | ---------------- |
| ID           | 主键                         |                  |
| REV_         | 数据版本                     |                  |
| TYPE         | 参数类型，类型有activiti提供 |                  |
| NAME         | 参数名称                     |                  |
| EXECUTION_ID | 参数对应执行流id             | ACT_RU_EXECUTION |
| PROC_INST_ID | 参数对应流程实例id           | ACT_RU_EXECUTION |
| TASK_ID      | 参数对应任务id               | ACT_RU_TASK      |
| BYTEARRAY_ID | 引用资源作为参数值的资源id   | ACT_GE_BYTEARRAY |
| DOUBLE       |                              |                  |
| LONG         |                              |                  |
| TEXT         |                              |                  |
| TEXT2        |                              |                  |

## 历史数据表（以ACT_HI开头）

### 流程实例表 （ACT_HI_PROCINST）

| 字段          | 说明                                                  | 关联表 |
| ------------- | ----------------------------------------------------- | ------ |
| START_ACT_ID  | 流程开始的活动的id（开始事件的id）                    |        |
| END_ACT_ID    | 流程结束的活动的id（结束事件的id）                    |        |
| DELETE_REASON | 流程实例删除原因，如果流程正常结束，该字段为completed |        |

### 流程明细表（ACT_HI_DETAIL）

### 历史任务表（ACT_HI_TASK）

与运行时任务表相似

### 历史行为表（ACT_HI_ACTINST）

记录每一个流程活动的实例，一个流程活动记为一条记录（包括开始事件，结束事件）

| 字段         | 说明                                        | 关联表         |
| ------------ | ------------------------------------------- | -------------- |
| ID           | 主键                                        |                |
| PROC_DEF_ID  | 流程定义id                                  | ACT_RE_PROCDEF |
| PROC_INST_ID | 流程实例id                                  |                |
| EXECUTION_ID | 执行流id                                    |                |
| ACT_ID       | 流程活动的id（bpmn的xml文件中活动的id）     |                |
| ACT_NAME     | 流程活动的名称（bpmn的xml文件中活动的name） |                |
| ACT_TYPE     | 活动类型                                    |                |
| ASSIGNEE     | 活动指派人                                  |                |
| START_TINE   | 开始时间                                    |                |
| END_TIME     | 结束事件                                    |                |
| DURATION     | 活动持续时间                                |                |

