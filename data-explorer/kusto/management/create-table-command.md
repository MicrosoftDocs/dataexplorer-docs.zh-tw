---
title: 。建立資料表-Azure 資料總管 |Microsoft Docs
description: 本文說明如何在 Azure 資料總管中建立資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 25554b5485562179d849e846fc5e71c587815e86
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108417"
---
# <a name="create-table"></a>.create 資料表

建立新的空白資料表。

此命令必須在特定資料庫的內容中執行。

需要[資料庫使用者](../management/access-control/role-based-authorization.md)權力。

**語法**

`.create``table` *TableName* （[columnName： columnType]，...） [`with` `(`[`docstring` `=` *FolderName* *Documentation* `,`檔] [ `folder`資料夾集`)`]] `=`

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

## <a name="create-merge-table"></a>。建立-合併資料表

建立新的資料表或擴充現有的資料表。 

此命令必須在特定資料庫的內容中執行。 

需要[資料庫使用者](../management/access-control/role-based-authorization.md)權力。

**語法**

`.create-merge``table` *TableName* （[columnName： columnType]，...） [`with` `(`[`docstring` `=` *FolderName* *Documentation* `,`檔] [ `folder`資料夾集`)`]] `=`

如果資料表不存在，則函式會與 ". create table" 命令完全相同。

如果資料表 T 存在，而且您傳送了 ". create-merge table T （<columns specification>）" 命令，則：

* 先前不存在<columns specification>于 t 中的任何資料行都會加入至 t 架構的結尾。
* 不在 T 中<columns specification>的任何資料行都不會從 t 移除。
* 中<columns specification>的任何資料行都存在於 T 中，但使用不同的資料類型會導致命令失敗。
