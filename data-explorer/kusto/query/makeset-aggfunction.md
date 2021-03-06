---
title: 'make_set ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) make_set。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 6e57f22dae1abef3838ed6f7065759c4a203e389
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303289"
---
# <a name="make_set-aggregation-function"></a>make_set ( # A1 (彙總函式) 

傳回一組相異值的 `dynamic` (JSON) 陣列，這些是 Expr 在群組中取得的值。

* 僅用於 [summarize](summarizeoperator.md) 內部彙總的內容。

## <a name="syntax"></a>語法

`summarize``make_set(` *Expr* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引數

* *Expr*：用於匯總計算的運算式。
* *MaxSize* 是選擇性的整數限制， (預設值為 *1048576*) 時傳回的元素數目上限。 MaxSize 值不能超過1048576。

> [!NOTE]
> `makeset()` 是函式的舊版和過時版本 `make_set` 。 舊版的預設限制為 *MaxSize* = 128。

## <a name="returns"></a>傳回

傳回一組相異值的 `dynamic` (JSON) 陣列，這些是 Expr 在群組中取得的值。
陣列的排序次序是未定義的。

> [!TIP]
> 若只要計算相異的值，請使用 [dcount ( # B1 ](dcount-aggfunction.md)

## <a name="example"></a>範例

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

:::image type="content" source="images/makeset-aggfunction/makeset.png" alt-text="顯示 Kusto 查詢在 Azure 資料總管中依大陸摘要國家/地區的表格":::

## <a name="see-also"></a>另請參閱

* 將 [`mv-expand`](./mvexpandoperator.md) 運算子用於相反的函式。
* [`make_set_if`](./makesetif-aggfunction.md) 運算子與相同 `make_set` ，但它也接受述詞。
