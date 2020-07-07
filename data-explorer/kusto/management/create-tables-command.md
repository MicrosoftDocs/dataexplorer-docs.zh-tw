---
title: 。建立資料表-Azure 資料總管 |Microsoft Docs
description: 本文說明如何在 Azure 資料總管中建立資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: ff8b3cfae6d3364b4d094f588c8130761fa5cb31
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966987"
---
# <a name="create-tables"></a>.create 資料表

建立新的空白資料表做為大量作業。

此命令必須在特定資料庫的內容中執行。

需要[資料庫使用者](../management/access-control/role-based-authorization.md)權力。

**語法**

`.create``tables` *TableName1* （[columnname： columnType]，...） [ `,` *TableName2* （[columnname： columnType]，...） ...] [ `with` `(` `folder` `=` *資料夾資料夾*] `)` ]

如果有任何資料表已經存在，此命令將會傳回 success。
 
**範例** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
