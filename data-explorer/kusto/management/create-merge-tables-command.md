---
title: 。建立-合併資料表-Azure 資料總管
description: 本文說明 Azure 資料總管中的 [建立-合併資料表] 命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2f80ea54ece66440dc7a6b40d9d571f04bd3e26b
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011511"
---
# <a name="create-merge-tables"></a>。建立-合併資料表

可讓您在特定資料庫的內容中，以單一大量作業建立和擴充現有資料表的架構。

> [!NOTE]
> 需要[資料庫使用者](../management/access-control/role-based-authorization.md)權力。
> 需要[資料表管理員許可權](../management/access-control/role-based-authorization.md)，才能擴充現有的資料表。

**語法**

`.create-merge``tables` *TableName1* （[columnname： columnType]，...） [ `,` *TableName2* （[columnname： columnType]，...） ...]

* 將會建立不存在的指定資料表。
* 已存在的指定資料表將會擴充其架構。
    * 不存在的資料行將會加入至現有資料表架構的_結尾_。
    * 未在命令中指定的現有資料行，將不會從現有資料表的架構中移除。
    * 在命令中使用與現有資料表架構不同的資料類型所指定的現有資料行，將會導致失敗。 將不會建立或擴充任何資料表。

**範例**

```kusto
.create-merge tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```

**傳回輸出**

| TableName | DatabaseName  | 資料夾 | DocString |
|-----------|---------------|--------|-----------|
| MyLogs    | TopComparison |        |           |
| MyUsers   | TopComparison |        |           |
