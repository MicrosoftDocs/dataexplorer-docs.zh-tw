---
title: dcount() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 dcount(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6f1df8c93d21b73be3753468708a119177d4d602
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030394"
---
# <a name="dcount-aggregation-function"></a>dcount() (聚合函數)

返回摘要組中標量表達式獲取的不同值數的估計值。

**語法**

...*Expr* `summarize` `dcount` `(` Expr [ 精度 ] ... `|` `,` *Accuracy* `)`

**引數**

* *Expr*: 需要計算其不同值的標量運算式。
* *精度*:`int`定義 請求的估計精度的可選文本。 有關支援的值,請參閱下文。 如果未指定,則使用預設值`1`。

**傳回**

傳回群組中 Expr** 之相異值數目的估計值。

**範例**

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="D 計數":::

**注意事項**

聚合`dcount()`函數主要用於估計大型集的基數。 它交易性能的準確性,因此可能會返回不同執行的結果(輸入的順序可能會影響其輸出)。

要準確計數群組的不同`V`值,應按`G`:

```kusto
T | summarize by V, G | summarize count() by G
```

此計算將需要大量的內部記憶體,因為不同的`V`值乘以`G`因此,它可能會導致記憶體錯誤或較大的執行時間。 `dcount()`提供快速可靠的替代方案:

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>估計精度

聚合`dcount()`函數使用[HyperLog (HLL) 演演演算法](https://en.wikipedia.org/wiki/HyperLogLog)的變體,該演演演算法對集合基數進行隨機估計。 該演演演算法提供了一個「旋鈕」,可用於平衡精度和執行時間/記憶體大小:

|精確度|錯誤 (%)|項目計數   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0.8|2<sup>14</sup>|
|       2|      0.4|2.<sup>16</sup>|
|       3|     0.28|2.<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> "項目計數"列是 HLL 實現中 1 位元組計數器的數量。

如果設置基數足夠小(精度級別`1`為 時為 1000 值),則該演演演算法包括一些執行完美計數(零誤差)的`2`規定;如果精度級別為 ,則為 8000 值。

錯誤約束是概率的,而不是理論約束。 該值是誤差分佈(西格瑪)的標準偏差,99.7% 的估計的相對誤差低於西格瑪的 3 倍。

下面描述了所有支援的準確性設置的相對估計誤差(百分比)的概率分佈函數:

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="hll 錯誤分配":::
