---
title: .創建表 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 .創建 Azure 數據資源管理員中的表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: f76c4d4a2780b58d9596537aea183d026d086fc5
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744038"
---
# <a name="create-tables"></a>.create 資料表

創建新的空表作為批量操作。

該命令必須在特定資料庫的上下文中運行。

需要[資料庫使用者權限](../management/access-control/role-based-authorization.md)。

**語法**

`.create``tables`*表名稱1([* 列名稱:欄類型],...)[`,` *表名稱2* ([列名稱:列類型], ...) ...

如果任何表已存在,該命令將返回成功。
 
**範例** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
