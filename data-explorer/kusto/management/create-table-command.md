---
title: 。建立資料表-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中建立資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: b071c4af6bc25650d18b1b66130941f73af551ff
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967089"
---
# <a name="create-table"></a>.create 資料表

建立新的空白資料表。

此命令必須在特定資料庫的內容中執行。

需要[資料庫使用者](../management/access-control/role-based-authorization.md)權力。

**語法**

`.create``table` *TableName* （[columnName： columnType]，...） [ `with` `(` [ `docstring` `=` *檔*] [ `,` `folder` `=` *資料夾*集] `)` ]

如果資料表已經存在，此命令將會傳回 success。

**範例** 

```kusto
.create table MyLogs ( Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32 ) 
```
 
**傳回輸出**

以 JSON 格式傳回資料表的架構，與相同：

```kusto
.show table MyLogs schema as json
```

> [!NOTE]
> 若要建立多個資料表，請使用[. create tables](create-tables-command.md)命令以獲得更好的效能，並降低叢集上的負載。
