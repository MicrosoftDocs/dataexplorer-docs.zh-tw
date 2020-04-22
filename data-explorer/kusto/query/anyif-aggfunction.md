---
title: anyif () (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的任意 if()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 813df821bad1b7e57315dad9bcd7b1387a2cd678
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030069"
---
# <a name="anyif-aggregation-function"></a>anyif () (聚合函數)

任意在[彙總運算符](summarizeoperator.md)中為每個組選擇一條記錄,謂詞為 true,並返回表達式的值,而不是每個此類記錄。

**語法**

`summarize`Expr ,*謂詞*) ' *Expr* `anyif` `(`

**引數**

* *Expr*: 從輸入中選擇要返回的每個記錄上的運算式。
* *謂詞*:謂詞:用於指示可以考慮評估哪些記錄。

**傳回**

聚合`anyif`函數返回為從匯總運算符的每個組隨機選擇的每個記錄計算的運算式值。 只能選擇*謂詞*返回 true 的記錄(如果謂詞不返回 true,則生成 null 值)。

**備註**

當您想要獲取複合組鍵每值一列的示例值時,此函數非常有用,但某些謂詞為 true。

如果存在非空/非空值,則函數將嘗試返回該值。

**範例**

顯示人口從 3 億到 6 億的隨機大陸:

```kusto
Continents | summarize anyif(Continent, Population between (300000000 .. 600000000))
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="任何 1":::
