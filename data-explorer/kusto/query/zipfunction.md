---
title: zip() - Azure 資料資源管理員 |微軟文件
description: 本文在 Azure 資料資源管理器中介紹 zip()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd407ce652d41471be5b30a15c2c09b9f608edb1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504228"
---
# <a name="zip"></a>zip()

函數`zip`接受任意數量`dynamic`的 陣列,並返回一個陣列,其元素都是一個陣列,包含同一索引的輸入陣列的元素。

**語法**

`zip(`*陣列1*`,`*陣列2*`, ... )`

**引數**

2 到 16 個動態陣列。

**範例**

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