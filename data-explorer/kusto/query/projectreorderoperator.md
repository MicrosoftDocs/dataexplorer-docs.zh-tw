---
title: 專案重新排序運算子 ─ Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的專案重新排序運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf56a69891f83aaabc12dbbcd8f758ecb963b493
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510841"
---
# <a name="project-reorder-operator"></a>project-reorder 運算子

重新排序結果輸出中的列。

```kusto
T | project-reorder Col2, Col1, Col* asc
```

**語法**

*T* `| project-reorder` *欄位名稱或模式*[`asc`|`desc`] [`,` ] [ ]

**引數**

* *T*: 輸入表。
* *欄名稱或圖案:* 新增到輸出的列或列通配符模式的名稱。
* 對於通配符模式:`asc``desc`以 升序或降序指定或使用其名稱的列指定或排序。 如果`asc``desc`或 未指定,順序由在源表中顯示的匹配列確定。

**傳回**

按運算符參數指定的順序包含列的表。 `project-reorder`不會重新命名或刪除表中的欄,因此,原始表中存在的所有欄位都將顯示在結果表中。

**注意事項**

- 在不明確*的列名稱或模式*匹配中,列將顯示在與模式匹配的第一個位置。
- 為指定`project-reorder`的列是可選的。 未顯式指定的列顯示為輸出表的最後一列。

* 用於[`project-away`](projectawayoperator.md)刪除欄位。
* 重新[`project-rename`](projectrenameoperator.md)命名欄位。


**範例**

重新排序具有三列(a、b、c)的表,以便第二列 (b) 首先出現。

```kusto
print a='a', b='b', c='c'
|  project-reorder b
```

|b|a|c|
|---|---|---|
|b|a|c|

重新排序表的列,以便以開頭的`a`列將顯示在其他列之前。

```kusto
print b = 'b', a2='a2', a3='a3', a1='a1'
|  project-reorder a* asc
```

|a1|a2|a3|b|
|---|---|---|---|
|a1|a2|a3|b|