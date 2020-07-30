---
title: series_periods_validate （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_periods_validate （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 24b47981e90c15e8a0f295d845ca28a03f324a88
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351296"
---
# <a name="series_periods_validate"></a>series_periods_validate()

檢查時間序列是否包含指定長度的定期模式。  

測量應用程式流量的度量通常是以每週或每日的時間來區分。 這段期間可以藉由執行 `series_periods_validate()` 檢查每週和每日時間來確認。

函式會採用包含時間序列動態陣列的資料行（通常是[make 系列](make-seriesoperator.md)運算子的結果輸出），以及一或多個 `real` 定義要驗證之期間長度的數位，做為輸入。

函式會輸出兩個數據行：
* *期間*：包含要驗證之期間（在輸入中提供）的動態陣列。
* *分數*：動態陣列，其中包含介於0和1之間的分數。 分數會顯示句點陣列中其各自位置的*某個期間的*重要性。

## <a name="syntax"></a>語法

`series_periods_validate(`*x* `,` *period1* [ `,` *period2* ] `,` 。 . . ] `)`

## <a name="arguments"></a>引數

* *x*：動態陣列純量運算式，這是數值的陣列，通常是[make 系列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算子的結果輸出。
* *period1*、 *period2*等等： `real` 指定要驗證之期間的數位（以 bin 大小的單位表示）。 例如，如果數列位於1h 的 bin 中，則每週期間為168的 bin。

> [!IMPORTANT]
> * 每個*period*引數的最小值為**4** ，而上限為輸入數列長度的一半。 對於超出這些界限的*period*引數，輸出分數將為**0**。
>
> * 輸入時間序列必須是一般的，也就是在常數的 bin 中匯總，而且如果是使用[make 系列](make-seriesoperator.md)所建立，則一律為大小寫。 否則，輸出就沒有意義。
> 
> * 函式最多可接受16個週期來進行驗證。

## <a name="example"></a>範例

下列查詢會內嵌應用程式流量的一個月快照集，每日匯總兩次（bin 大小為12小時）。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="數列期間":::

如果您 `series_periods_validate()` 在此系列上執行以驗證每週期間（14個點長），則會產生高分數，而當您驗證5天的期間（10點長）時，其分數為**0** 。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| 數列 \_ 週期 \_ 驗證 \_ y \_ 期間  | 數列 \_ 週期 \_ 驗證 \_ y \_ 分數 |
|-------------|-------------------|
| [14.0，10.0] | [0.84，0.0]  |
