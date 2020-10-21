---
title: 'zip ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 zip ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33b914babb8c197f997326cd6dd44654c05d1d9d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253142"
---
# <a name="zip"></a>zip()

此函式 `zip` 會接受任意數目的 `dynamic` 陣列，並傳回陣列，其專案為每個陣列，其中每一個陣列都包含相同索引的輸入陣列元素。

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