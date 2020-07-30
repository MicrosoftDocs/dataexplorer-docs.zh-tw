---
title: zip （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 zip （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 28fc477d4dfc5432434261e493f36985514ea28b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350548"
---
# <a name="zip"></a>zip()

函式 `zip` 會接受任意數目的 `dynamic` 陣列，並傳回陣列，其專案為每個陣列，其中包含相同索引之輸入陣列的元素。

## <a name="syntax"></a>語法

`zip(`*array1* `,`*array2*`, ... )`

## <a name="arguments"></a>引數

介於2到16個動態陣列之間。

## <a name="examples"></a>範例

下列範例會傳回 `[[1,2],[3,4],[5,6]]`：

```kusto
print zip(dynamic([1,3,5]), dynamic([2,4,6]))
```

下列範例會傳回 `[["A",{}], [1,"B"], [1.5, null]]`：

```kusto
print zip(dynamic(["A", 1, 1.5]), dynamic([{}, "B"]))
```

下列範例會傳回 `[[1,"one"],[2,"two"],[3,"three"]]`：

```kusto
datatable(a:int, b:string) [1,"one",2,"two",3,"three"]
| summarize a = make_list(a), b = make_list(b)
| project zip(a, b)
```