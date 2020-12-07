---
title: distinct 運算子 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的 distinct 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 86b8617698f3708edcebbc1c2c4bd1732054600f
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513160"
---
# <a name="distinct-operator"></a>distinct 運算子

產生資料表，其中以不同方式結合提供的輸入資料表資料列。 

```kusto
T | distinct Column1, Column2
```

產生資料表，其中以不同方式結合輸入資料表中的所有資料列。

```kusto
T | distinct *
```

## <a name="example"></a>範例

顯示水果和價格的相異組合。

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="兩個資料表。其中一個包含廠商、水果類型和價格，並有些重複的水果價格組合。第二個資料表只列出唯一的組合。":::

**注意事項**

* 不同於 `summarize by ...`，`distinct` 運算子支援提供星號 (`*`) 作為群組索引鍵，讓較寬的資料表更容易使用。
* 如果 group by 索引鍵屬於高基數，使用 `summarize by ...` 搭配[隨機策略](shufflequery.md)可能會很有用。