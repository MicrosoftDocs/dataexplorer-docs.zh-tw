---
title: 延伸運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的擴展運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4b9b7bdb9488b0e9d1b72b3e0ab4782020c9b841
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515584"
---
# <a name="extend-operator"></a>extend 運算子

創建計算列並將其追加到結果集中。

```kusto
T | extend duration = endTime - startTime
```

**語法**

*T* `| extend` =*欄位* | `(`*名稱*=`,` ...][ 運算`,`式*Expression*`)``=`]

**引數**

* *T*: 輸入表格結果集。
* *欄位名稱:* 選。 要添加或更新的列的名稱。 如果省略,將生成名稱。 如果*運算式*傳回多個列,則可以在括弧中指定列名稱的清單。 在這種情況下 *,將為表達式*的輸出列指定名稱,刪除輸出列的其餘部分(如果有)。 如果未指定列名稱的清單,則所有*運算式*的輸出列都將與生成的名稱一起添加到輸出中。
* *運算式:* 對輸入列的計算。

**傳回**

輸入表格結果集的複本,以便:
1. 輸入`extend`中已存在的列名稱將被刪除並追加為其新的計算值。
2. 輸入中不存在的`extend`列名稱將作為其新的計算值追加。

**技巧**

* 運算子`extend`向沒有索引的輸入結果集添加新列。 **not** 在大多數情況下,如果新列設置為與具有索引的現有表列完全相同,則 Kusto 可以自動使用現有索引。 但是,在某些複雜方案中,此傳播不會完成。 在這種情況下,如果目標是重新命名欄,則改用[`project-rename`運算子](projectrenameoperator.md)。

**範例**

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

可以使用[series_stats](series-statsfunction.md)函數返回多個列。