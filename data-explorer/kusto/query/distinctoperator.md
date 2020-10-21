---
title: distinct 運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的相異操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ad90d9765b7ff708f7b40b65564b3944de5c84df
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245185"
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