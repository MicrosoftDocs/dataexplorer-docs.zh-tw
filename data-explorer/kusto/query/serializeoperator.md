---
title: 序列化操作員-Azure 資料總管
description: 本文說明 Azure 資料總管中的序列化運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 603d69be34ea6040f6c864958eea86e570204d04
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250187"
---
# <a name="serialize-operator"></a>serialize 運算子

標記輸入資料列集的順序可以安全地用於視窗函數。

運算子具有宣告式意義。 它會將輸入資料列集標示為序列化 (排序) ，以便將 [視窗](./windowsfunctions.md) 函式套用至該資料列。

```kusto
T | serialize rn=row_number()
```

## <a name="syntax"></a>語法

`serialize`[*Name1* `=`*運算式 1* ： [ `,` *Name2* `=` *運算式 2*] ...]

* *名稱* / *Expr*組類似于[擴充運算子](./extendoperator.md)中的配對。

## <a name="example"></a>範例

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

下列運算子的輸出資料列集標示為未序列化。

[範例](./sampleoperator.md)、 [樣本相異](./sampledistinctoperator.md)、 [相異](./distinctoperator.md)、 [聯結](./joinoperator.md)、 [最上層嵌套](./topnestedoperator.md)、 [計數](./countoperator.md)、 [摘要](./summarizeoperator.md)、 [facet](./facetoperator.md)、 [mv-展開](./mvexpandoperator.md)、 [評估](./evaluateoperator.md)、 [縮減依據](./reduceoperator.md)、 [構成系列](./make-seriesoperator.md)

所有其他運算子都會保留序列化屬性。 如果輸入資料列集已序列化，則輸出資料列集也會序列化。
