---
title: pgsql
date: 2021-05-14 08:56:46
tags:
---

## 查询json字段

字段内容

``````json
"rule":{
    "tags": {
        "target": "logon"
    },
    "time": "2019-11-15 10:00:00",
    "method": "scheduled",
    "source": "backend",
    "aliases": [],
    "sendType": "tag",
    "registrationIds": []
}
``````

查询rule字段sendType = ‘tag’

``````sql
 SELECT
        uuid,create_user,create_time,update_user,update_time,delete_flag,NAME,type,push,rule, STATUS 
    FROM
        jp_push jpt 
    WHERE
        jpt.delete_flag = 1 
        AND ( jpct.rule :: json ->> 'sendType' ) :: text = 'tag' 
        AND jpt.NAME = 'testname' 
    ORDER BY jpt.create_time ASC LIMIT 20 OFFSET 0
``````

