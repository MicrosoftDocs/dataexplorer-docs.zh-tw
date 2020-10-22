---
title: 專案-保留操作員-Azure 資料總管
description: 本文說明 Azure 資料總管中的專案保留運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/21/2020
ms.openlocfilehash: 2efc06ad701cc734ffad0bec7d89e3d3ef1d390d
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356711"
---
# <a name="project-keep-operator"></a>專案保留運算子

從輸入中選取要保留在輸出中的資料行。

```kusto
T | project-keep price, quantity, zz*
```

結果中的資料行順序取決於資料表中的原始順序。 系統只會保留指定為引數的資料行。 系統會從結果中排除其他資料行。 另請參閱 [`project`](projectoperator.md) 。

## <a name="syntax"></a>語法

*T* `| project-keep` *ColumnNameOrPattern* [ `,` ...]

## <a name="arguments"></a>引數

* *T*：輸入資料表
* *ColumnNameOrPattern：* 要保留在輸出中的資料行或資料行萬用字元模式的名稱。

## <a name="returns"></a>傳回

具有命名為引數之資料行的資料表。 包含與輸入資料表相同的資料列數目。

> [!TIP]
>* 若要重新命名資料行，請使用 [`project-rename`](projectrenameoperator.md) 。
>* 若要重新排列資料行，請使用 [`project-reorder`](projectreorderoperator.md) 。
>* 您可以有 `project-keep` 存在於原始資料表中的任何資料行，或是在查詢中計算出的資料行。

## <a name="example"></a>範例

輸入資料表 `T` 有三個類型為 `long` 的資料行：`A`、`B` 和 `C`。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A1:long, A2:long, B:long) [1, 2, 3]
| project-keep A*    // Keeps only columns A1 and A2 in the output
```

|A1|A2|
|---|---|
|1|2|

## <a name="see-also"></a>請參閱

若要從輸出中選擇要排除的輸入資料行，請使用 [專案離開](projectawayoperator.md)。
