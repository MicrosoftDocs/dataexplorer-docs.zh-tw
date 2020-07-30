---
title: top 運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 top 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e8c917d0899ada7d65cacd8e1433027535338e24
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340372"
---
# <a name="top-operator"></a>top 運算子

傳回依指定資料行排序的前*N*筆記錄。

```kusto
T | top 5 by Name desc nulls last
```

## <a name="syntax"></a>語法

*T* `| top` *NumberOfRows* `by` *運算式*[ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ]

## <a name="arguments"></a>引數

* *NumberOfRows*：要傳回的*T*資料列數目。 您可以指定任何數值運算式。
* *Expression*：用來排序的純量運算式。 值的類型必須是數值、日期、時間或字串。
* `asc` 或 `desc` (預設值) 可能會出現，以控制實際上是從範圍的「下限」或「上限」進行選取。
* `nulls first`（order 的預設值 `asc` ）或 `nulls last`（order 的預設值 `desc` ）可能會出現，以控制 null 值是否會在範圍的開頭或結尾。


**提示**

* `top 5 by name`相當於 `sort by name | take 5` 來自語義和效能角度的運算式。
* 使用[最上層的](topnestedoperator.md)運算子來產生階層式（嵌套）的最大結果。