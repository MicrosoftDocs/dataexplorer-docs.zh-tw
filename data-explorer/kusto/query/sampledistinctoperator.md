---
title: 範例-相異運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的相異範例運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfd8385a5dc8f959e1fe195bfe333a6868f55cb4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242739"
---
# <a name="sample-distinct-operator"></a>sample-distinct 運算子

傳回單一資料行，其中包含所要求資料行的相異值數目上限。 

預設的 (和目前只有) 的類別類別會嘗試儘快傳回答案 (而不是嘗試建立公平範例) 

```kusto
T | sample-distinct 5 of DeviceId
```

## <a name="syntax"></a>語法

*T* `| sample-distinct` *NumberOfValues* `of` *ColumnName*

## <a name="arguments"></a>引數
* *NumberOfValues*：要傳回的 *T* 的數位相異值。 您可以指定任何數值運算式。

**提示**

 藉由 `sample-distinct` 在 let 語句中放入 let 語句並稍後使用運算子進行篩選，可以派上用場的範例 `in` (請參閱範例)  

 如果您想要使用最前面的值而不只是範例，您可以使用 [hitters](tophittersoperator.md) 運算子 

 如果您想要 (資料列的範例，而不是特定資料行) 的值，請參閱 [範例運算子](sampleoperator.md)

## <a name="examples"></a>範例  

取得人口的10個相異值

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | sample-distinct 10 of EpisodeId

```

為母體取樣並在知道摘要不會超過查詢限制的情況下執行進一步的運算。 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents 
| where EpisodeId in (sampleEpisodes) 
| summarize totalInjuries=sum(InjuriesDirect) by EpisodeId
```
