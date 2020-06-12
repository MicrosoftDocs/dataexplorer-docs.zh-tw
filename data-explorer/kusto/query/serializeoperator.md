---
title: 序列化運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的序列化運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 708a5ccd5f8402dedb074a6ab8c17b1d7762839c
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717116"
---
# <a name="serialize-operator"></a>serialize 運算子

標記輸入資料列集的順序可安全地用於視窗函數。

運算子具有宣告式意義。 它會將輸入資料列集標示為已序列化（已排序），以便將視窗函式套用至該[工作](./windowsfunctions.md)表。

```kusto
T | serialize rn=row_number()
```

**語法**

`serialize`[*Name1* `=`*運算式 1* [ `,` *Name2* `=` *運算式 2*] ...]

* *名稱* / *Expr*配對與[extend 運算子](./extendoperator.md)中的配對類似。

**範例**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| serialize

Traces
| where ActivityId == "479671d99b7b"
| serialize rn = row_number()
```

下列運算子的輸出資料列集標示為已序列化。

[range](./rangeoperator.md)、 [sort](./sortoperator.md)、 [order](./orderoperator.md)、 [top](./topoperator.md)、 [top-hitters](./tophittersoperator.md)、 [getschema](./getschemaoperator.md)。

下列運算子的輸出資料列集標示為非序列化。

[範例](./sampleoperator.md)，[範例相異](./sampledistinctoperator.md)，[相異](./distinctoperator.md)，[聯結](./joinoperator.md)，[頂端嵌套](./topnestedoperator.md)，[計數](./countoperator.md)，[摘要](./summarizeoperator.md)， [facet](./facetoperator.md)， [mv-展開](./mvexpandoperator.md)，[評估](./evaluateoperator.md)，[減少依據](./reduceoperator.md)， [make 系列](./make-seriesoperator.md)

所有其他運算子都會保留序列化屬性。 如果輸入資料列集已序列化，則輸出資料列集也會序列化。
