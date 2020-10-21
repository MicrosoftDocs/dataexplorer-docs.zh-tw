---
title: 'series_periods_validate ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_periods_validate。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: dbf87c65668289e58fab7280a4b70f04152bcba8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242028"
---
# <a name="series_periods_validate"></a>series_periods_validate()

檢查時間序列是否包含指定長度的定期模式。  

測量應用程式流量的計量通常是以每週或每日期間為特徵。 您可以藉由執行 `series_periods_validate()` 每週和每日一次的檢查來確認這段時間。

此函式會以包含時間序列動態陣列的資料行作為輸入， (通常是產生 [序列](make-seriesoperator.md) 運算子) 的輸出，以及 `real` 定義要驗證之期間長度的一或多個數位。

此函數會輸出兩個數據行：
* *句號*：動態陣列，包含輸入) 中所提供的驗證 (期間。
* *分數*：包含0和1之間分數的動態陣列。 分數會顯示 *句號陣列中* 其各自位置的某個期間的重要性。

## <a name="syntax"></a>語法

`series_periods_validate(`*x* `,` *period1* [ `,` *period2* ] `,` 。 . . ] `)`

## <a name="arguments"></a>引數

* *x*：動態陣列純量運算式，這是數值的陣列，通常是 [構成數列](make-seriesoperator.md) 或 [make_list](makelist-aggfunction.md) 運算子的結果輸出。
* *period1*、 *period2*等： `real` 指定要驗證之期間（以 bin 大小為單位）的數位。 例如，如果數列在 1h bin 中，則每週期間是168的 bin。

> [!IMPORTANT]
> * 每個 *期間* 引數的最大值為 **4** ，而最大值為輸入數列長度的一半。 若為這些界限以外的 *句號* 引數，輸出分數將會是 **0**。
>
> * 輸入時間序列必須是一般的，也就是在常數 bin 中匯總，而且如果是使用 [make 系列](make-seriesoperator.md)建立的，則一律是大小寫。 否則，輸出就沒有意義。
> 
> * 函數最多可接受16個期間來進行驗證。

## <a name="example"></a>範例

下列查詢會內嵌一個月的應用程式流量快照集，每日匯總兩次 (分類大小為12小時) 。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="系列期間":::

如果您 `series_periods_validate()` 在此系列上執行以驗證每週期間 (14 點) 它會導致高度分數，並在您驗證五天期間（ (10 個點的長) ）時，將會有 **0** 分數。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| 數列 \_ 期間 \_ 驗證 \_ y \_ 句號  | 數列 \_ 期間 \_ 驗證 \_ y \_ 分數 |
|-------------|-------------------|
| [14.0，10.0] | [0.84，0.0]  |
