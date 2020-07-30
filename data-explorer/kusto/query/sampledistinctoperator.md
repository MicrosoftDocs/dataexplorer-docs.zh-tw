---
title: 範例-相異運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的範例相異運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3cb1de08604964d4d71c5868ef7564c728b1f2c4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351534"
---
# <a name="sample-distinct-operator"></a>sample-distinct 運算子

傳回單一資料行，其中包含所要求資料行的相異值數目上限。 

運算子的預設值（目前只有）類別會嘗試儘快傳回解答（而不是嘗試建立合理的範例）

```kusto
T | sample-distinct 5 of DeviceId
```

## <a name="syntax"></a>語法

*T* `| sample-distinct` *NumberOfValues* `of` *ColumnName*

## <a name="arguments"></a>引數
* *NumberOfValues*：要傳回之*T*的數位相異值。 您可以指定任何數值運算式。

**提示**

 藉由放 `sample-distinct` 入 let 語句，並在稍後使用運算子進行篩選 `in` （請參閱範例），可以派上用場的樣本。 

 如果您想要最前面的值，而不只是範例，您可以使用[hitters](tophittersoperator.md)運算子 

 如果您想要取樣資料列（而非特定資料行的值），請參閱[範例運算子](sampleoperator.md)

## <a name="examples"></a>範例  

從人口取得10個相異值

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
