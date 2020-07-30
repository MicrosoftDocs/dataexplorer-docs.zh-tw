---
title: make_set （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 make_set （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: c85738928aa65bf2a4476f10afa065c2a8ca1faf
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346910"
---
# <a name="make_set-aggregation-function"></a>make_set （）（彙總函式）

傳回一組相異值的 `dynamic` (JSON) 陣列，這些是 Expr** 在群組中取得的值。

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

`summarize``make_set(` *Expr* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引數

* *Expr*：匯總計算的運算式。
* *MaxSize*是傳回的最大專案數目的選擇性整數限制（預設為*1048576*）。 MaxSize 值不能超過1048576。

> [!NOTE]
> 此函式的舊版和過時變體： `makeset()` 具有*MaxSize* = 128 的預設限制。

## <a name="returns"></a>傳回

傳回一組相異值的 `dynamic` (JSON) 陣列，這些是 Expr** 在群組中取得的值。
陣列的排序次序未定義。

> [!TIP]
> 若只要計算相異值，請使用[dcount （）](dcount-aggfunction.md)

## <a name="example"></a>範例

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

:::image type="content" source="images/makeset-aggfunction/makeset.png" alt-text="Makeset":::

**另請參閱**

* [`mv-expand`](./mvexpandoperator.md)針對相反的函式使用運算子。
* [`make_set_if`](./makesetif-aggfunction.md)運算子類似于 `make_set` ，不同之處在于它也接受述詞。