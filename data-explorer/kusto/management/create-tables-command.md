---
title: . 建立資料表-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中建立資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: 28ee7e462245497dd14d3a14a1c0703cc71a8933
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321585"
---
# <a name="create-tables"></a>.create 資料表

建立新的空白資料表做為大量作業。

命令必須在特定資料庫的內容中執行。

需要 [資料庫使用者](../management/access-control/role-based-authorization.md)權力。

**語法**

`.create``tables` *TableName1* ( [columnName： columnType]，... ) [ `,` *TableName2* ( [columnName： columnType]，... ) ...] [ `with` `(` `folder` `=` *資料夾資料夾*] `)` ]

如果有任何資料表已經存在，命令將會傳回 success。
 
**範例** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
