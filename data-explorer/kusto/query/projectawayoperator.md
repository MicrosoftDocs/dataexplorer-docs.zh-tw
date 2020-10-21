---
title: 專案離開操作員-Azure 資料總管
description: 本文說明 Azure 資料總管中的專案離開操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a708a83e4bef1c1d9b774f0304e2dd8c7cba8cda
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244797"
---
# <a name="project-away-operator"></a>project-away 運算子

在輸入中選取要從輸出中排除的資料行

```kusto
T | project-away price, quantity, zz*
```

結果中的資料行順序取決於資料表中的原始順序。 只有指定為引數的資料行才會被卸載。 結果中會包含其他資料行。  (另請參閱 `project`)。

## <a name="syntax"></a>語法

*T* `| project-away` *ColumnNameOrPattern* [ `,` ...]

## <a name="arguments"></a>引數

* *T*：輸入資料表
* *ColumnNameOrPattern：* 要從輸出中移除之資料行或資料行萬用字元模式的名稱。

## <a name="returns"></a>傳回

具有未命名為引數之資料行的資料表。 包含與輸入資料表相同的資料列數目。

**提示**

* [`project-rename`](projectrenameoperator.md)如果您想要重新命名資料行，請使用。
* [`project-reorder`](projectreorderoperator.md)如果您想要重新排列資料行，請使用。

* 您可以有 `project-away` 存在於原始資料表中的任何資料行，或是在查詢中計算出的資料行。


## <a name="examples"></a>範例

輸入資料表 `T` 有三個類型為 `long` 的資料行：`A`、`B` 和 `C`。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|A|B|
|---|---|
|1|2|

正在移除以 ' a ' 開頭的資料行。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print  a2='a2', b = 'b', a3='a3', a1='a1'
|  project-away a* 
```

|b|
|---|
|b|

