---
layout: post
title: "实时计算平台规划"
subtitle: ""
author: "Marshall"
header-img: "img/post-bg-2015.jpg"
header-mask: 0.3
tags:
  - 自我救赎
  - 实时计算
  - 平台化
  - 开源
---

####Name plz?
> Acorn or Chestnut or ?

## 实时计算平台规划
---
###v0.1	
1. 支持rest api提交sql任务
2. 支持通过gauss开发节点提交SQL，执行流式ETL
3. 支持后台sql校验表是否存在
4. 支持checkpoint自动配置，开关式
5. 支持选择提交模式（standalone yarn-per-job）
6. 支持自定义任务配置（资源、并发等）
7. 支持日志在gauss中查看或提供ApplicationId跳转查看
8. 支持任务指标监控（auto report metrics）
---
###v0.2	
1. 支持在线debug（定义sql后，debug查看输入xx后输出xxx）
2. 支持catalog管理
3. 支持表管理（catalog表，CRUD）
4. 支持UDF管理
5. 支持SQL Parser解析sql
6. 支持优化建议（是否可以 DISTINCT FILTER、LOCAL GLOBAL、mini Batch、split distinct AGGR）
---
###v0.3	
1. 交互式任务配置
2. 支持jdbc方式提交sql任务
3. 依赖表下推创建
4. 数据血缘查看
5. 报警和重新拉起
6. 支持savepoint触发
7. 支持选择提交模式（application）