---
title: 專案外操作員-Azure 資料總管
description: 本文說明 Azure 資料總管中的專案離開運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 40bc5eafee803123ea1d73e763c32b5210f741ca
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346043"
---
# <a name="project-away-operator"></a>project-away 運算子

在輸入中選取要從輸出排除的資料行

```kusto
T | project-away price, quantity, zz*
```

結果中的資料行順序取決於其在資料表中的原始順序。 只會卸載指定為引數的資料行。 其他資料行則包含在結果中。  (另請參閱 `project`)。

## <a name="syntax"></a>語法

*T* `| project-away` *ColumnNameOrPattern* [ `,` ...]

## <a name="arguments"></a>引數

* *T*：輸入資料表
* *ColumnNameOrPattern：* 要從輸出中移除之資料行或資料行萬用字元模式的名稱。

## <a name="returns"></a>傳回

包含未命名為引數之資料行的資料表。 包含與輸入資料表相同的資料列數目。

**提示**

* [`project-rename`](projectrenameoperator.md)如果您想要重新命名資料行，請使用。
* [`project-reorder`](projectreorderoperator.md)如果您想要重新排列資料行，請使用。

* 您可以在 `project-away` 原始資料表中出現的任何資料行，或做為查詢一部分計算的資料行。


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

移除以 ' a ' 開頭的資料行。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print  a2='a2', b = 'b', a3='a3', a1='a1'
|  project-away a* 
```

|b|
|---|
|b|

