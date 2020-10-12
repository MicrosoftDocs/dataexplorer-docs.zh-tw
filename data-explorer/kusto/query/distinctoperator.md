---
title: distinct 運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的相異操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ffdd20c6d0c078cd3a7ecaa0d0fb2dac131ddda5
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941769"
---
# <a name="distinct-operator"></a>distinct 運算子

使用輸入資料表所提供資料行的相異組合來產生資料表。 

```kusto
T | distinct Column1, Column2
```

使用輸入資料表中所有資料行的相異組合來產生資料表。

```kusto
T | distinct *
```

## <a name="example"></a>範例

顯示水果和價格的相異組合。

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="兩個數據表。其中一個具有供應商、水果類型和價格，並重複使用一些水果價格組合。第二個數據表只會列出唯一的組合。":::

**備註**

* 與不同的是 `summarize by ...` ， `distinct` 運算子支援提供星號 (`*`) 作為群組索引鍵，讓您更輕鬆地使用寬型資料表。
* 如果群組依據索引鍵的基數很高，使用 `summarize by ...` 隨機的 [策略](shufflequery.md) 可能會很有用。