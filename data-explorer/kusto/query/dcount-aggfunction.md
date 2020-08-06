---
title: 'dcount ( # A1 (彙總函式) -Azure 資料總管'
description: '本文說明 Azure 資料總管中的 dcount ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 45ab913fdc659444ac578ca725e2afb24256a38b
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803704"
---
# <a name="dcount-aggregation-function"></a>dcount ( # A1 (彙總函式) 

傳回摘要群組中純量運算式所使用之相異值數目的估計。

> [!NOTE]
> `dcount()`彙總函式主要適用于估計大型集合的基數。 它會針對精確度來交易效能，而且可能會傳回不同執行之間的結果。 輸入的順序可能會影響其輸出。

## <a name="syntax"></a>語法

... `|` `summarize` `dcount` `(`*`Expr`*[, *`Accuracy`*]`)` ...

## <a name="arguments"></a>引數

* *Expr*：要計算其相異值的純量運算式。
* *精確度*：定義所 `int` 要求估計精確度的選擇性常值。 請參閱下方的支援值。 如果未指定，則 `1` 會使用預設值。

## <a name="returns"></a>傳回

傳回群組中相異值數目的估計 *`Expr`* 。

## <a name="example"></a>範例

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="D 計數":::

取得分組依據之相異值的確切計數 `V` `G` 。

```kusto
T | summarize by V, G | summarize count() by G
```

這項計算需要大量的內部儲存體，因為的相異值 `V` 會乘以的相異值數目 `G` 。
這可能會導致記憶體錯誤或執行時間很長。 
`dcount()`提供快速且可靠的替代方法：

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>估計精確度

`dcount()`彙總函式會使用[HYPERLOGLOG (HLL) 演算法](https://en.wikipedia.org/wiki/HyperLogLog)的變體，它會對設定的基數進行隨機估計。 此演算法提供「旋鈕」，可用來平衡每個記憶體大小的精確度和執行時間：

|精確度|錯誤 (% ) |專案計數   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0.8|2<sup>14</sup>|
|       2|      0.4|2<sup>16</sup>|
|       3|     0.28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> 「專案計數」資料行是 HLL 執行中的1個位元組計數器數目。

如果設定的基數夠小，此演算法就會包含一些執行最佳計數的規定 (零錯誤) 。
* 當精確度層級為時 `1` ，會傳回1000值
* 當精確度層級為時 `2` ，會傳回8000值

所系結的錯誤是概率，而不是理論上的界限。 值是 (sigma) 的錯誤散發標準差，而99.7% 的估計將會有 3 x sigma 之下的相對錯誤。

下圖顯示所有支援的精確度設定之相對估計錯誤的機率分佈函數（以百分比表示）：

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="hll 錯誤散發":::
