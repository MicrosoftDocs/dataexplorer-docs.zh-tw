---
title: 'dcount ( # A1 (彙總函式) -Azure 資料總管'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) 的 dcount。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b35bb7944e894256056e03eb756ac85cf1354ba8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247695"
---
# <a name="dcount-aggregation-function"></a>dcount ( # A1 (彙總函式) 

傳回摘要群組中純量運算式所採用相異值數目的估計值。

> [!NOTE]
> `dcount()`彙總函式主要適用于估計大型集合的基數。 它會針對精確度來交易效能，而且可能會傳回不同執行之間的結果。 輸入的順序可能會影響其輸出。

## <a name="syntax"></a>語法

... `|` `summarize` `dcount` `(`*`Expr`*[, *`Accuracy`*]`)` ...

## <a name="arguments"></a>引數

* *Expr*：要計算其相異值的純量運算式。
* *精確度*：定義所 `int` 要求估計精確度的選擇性常值。 請參閱下文以取得支援的值。 如果未指定，則 `1` 會使用預設值。

## <a name="returns"></a>傳回

傳回群組中相異值數目的估計值 *`Expr`* 。

## <a name="example"></a>範例

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="D 計數":::

取得分組依據之相異值的確切計數 `V` `G` 。

```kusto
T | summarize by V, G | summarize count() by G
```

這項計算需要大量的內部記憶體，因為相異的值 `V` 乘以的相異值數目 `G` 。
這可能會導致記憶體錯誤或執行時間很長。 
`dcount()`提供快速且可靠的替代方案：

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>估計精確度

`dcount()`彙總函式會使用[HYPERLOGLOG (HLL) 演算法](https://en.wikipedia.org/wiki/HyperLogLog)的變體，這會執行設定基數的隨機估計。 此演算法提供的「旋鈕」可用來平衡每個記憶體大小的精確度和執行時間：

|精確度|錯誤 (% ) |專案計數   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0.8|2<sup>14</sup>|
|       2|      0.4|2<sup>16</sup>|
|       3|     0.28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> [專案計數] 資料行是 HLL 執行中的1位元組計數器數目。

如果設定的基數夠小，演算法就會包含一些可 (零錯誤) 的零錯誤的設定：
* 當精確度層級為時 `1` ，會傳回1000值
* 當精確度層級為時 `2` ，會傳回8000值

系結的錯誤是概率，而不是理論上系結。 值是 (sigma) 的誤差分佈標準差，而99.7% 的估計值在 3 x sigma 下會有相關的錯誤。

下圖顯示所有支援的精確度設定之相對估計誤差的機率分佈函數（以百分比表示）：

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="D 計數":::
