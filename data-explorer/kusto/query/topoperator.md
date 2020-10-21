---
title: top 運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 top 操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aadb0b41e666b5b0e90fa33b7ddc30e363e985c3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241229"
---
# <a name="top-operator"></a>top 運算子

傳回依指定資料行排序的前 *N* 筆記錄。

```kusto
T | top 5 by Name desc nulls last
```

## <a name="syntax"></a>語法

*T* `| top` *NumberOfRows* `by` *Expression* [ `asc`  |  `desc` ] [] `nulls first`  |  `nulls last`

## <a name="arguments"></a>引數

* *NumberOfRows*：要傳回的 *T* 的資料列數目。 您可以指定任何數值運算式。
* *運算式*：用來排序的純量運算式。 值的類型必須是數值、日期、時間或字串。
* `asc` 或 `desc` (預設值) 可能會出現，以控制實際上是從範圍的「下限」或「上限」進行選取。
* `nulls first` (order) 的預設值 `asc` 或 `nulls last` (順序的預設值 `desc`) 可能會顯示 null 值是否會在範圍的開頭或結尾。

> [!TIP]
> `top 5 by name` 相當於 `sort by name | take 5` 語義和效能觀點的運算式。

## <a name="see-also"></a>另請參閱 

* 使用 [top 嵌套](topnestedoperator.md) 運算子來產生階層式 (嵌套) 的前幾個結果。
