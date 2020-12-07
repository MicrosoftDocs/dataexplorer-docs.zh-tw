---
title: dcount() (彙總函式) - Azure 資料總管
description: 本文描述 Azure 資料總管中的 dcount() (彙總函式)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 947ab0af6a5aaa98bb07b08005b940fdf2ce6ae5
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513177"
---
# <a name="dcount-aggregation-function"></a>dcount() (彙總函式)

傳回摘要群組中純量運算式所使用的相異值數目預估。

> [!NOTE]
> `dcount()` 彙總函式主要適用於估計大型集合的基數。 其會以效能換取精確度，而且會因為不同的執行傳回不同的結果。 輸入的順序可能會影響其輸出。

## <a name="syntax"></a>語法

... `|` `summarize` `dcount` `(`*`Expr`*[, *`Accuracy`*]`)` ...

## <a name="arguments"></a>引數

* *Expr*：要計算其相異值的純量運算式。
* *精確度*：選擇性的 `int` 常值，可定義要求的估計精確度。 請參閱下方的支援值。 如果未指定，則會使用預設值 `1`。

## <a name="returns"></a>傳回

傳回群組中 `Expr` 的相異值數目預估。

## <a name="example"></a>範例

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="D count":::

取得依 `G` 分組的 `V` 相異值確切計數。

```kusto
T | summarize by V, G | summarize count() by G
```

這項計算需要大量的內部儲存體，因為 `V` 的相異值會乘以 `G` 相異值的數目。
這可能會導致記憶體錯誤或執行時間過長。 
`dcount()` 提供快速且可靠的替代方法：

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>估計精確度

`dcount()` 彙總函式使用 [HyperLogLog (HLL) 演算法](https://en.wikipedia.org/wiki/HyperLogLog)的變異，其會對設定的基數進行隨機估計。 此演算法提供 "knob"，可用來平衡每個記憶體大小的精確度和執行時間：

|精確度|錯誤 (%)|項目計數   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0.8|2<sup>14</sup>|
|       2|      0.4|2<sup>16</sup>|
|       3|     0.28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> 「項目計數」資料行是 HLL 實作中 1 位元組計數器的數目。

如果設定的基數夠小，此演算法就會包含一些執行最佳計數 (零錯誤) 的條款：
* 當精確度層級為 `1` 時，會傳回 1000 個值
* 當精確度層級為 `2` 時，會傳回 8000 個值

誤差界限是概率，不是理論上的界限。 值會是誤差分佈的標準差 (sigma)，而 99.7% 的估計會有 3 x sigma 以下的相對誤差。

下圖會針對所有支援的精確度設定，說明相對估計誤差的機率分佈函式 (以百分比表示)：

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="hll 誤差分佈":::
