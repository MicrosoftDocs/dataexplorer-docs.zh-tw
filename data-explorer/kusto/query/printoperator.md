---
title: 列印運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的列印操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2019
ms.openlocfilehash: d5788ee937fe110b63a8f137fdab0790eb7cb37e
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373212"
---
# <a name="print-operator"></a>print 運算子

輸出含有一或多個純量運算式的單一資料列。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

**語法**

`print`[*ColumnName* `=` ]*ScalarExpression* ['，' ...]

**引數**

* *ColumnName*：要指派給輸出單數資料行的選項名稱。
* *ScalarExpression*：要評估的純量運算式。

**傳回**

單一資料行的單一資料列資料表，其單一資料格具有已評估之*ScalarExpression*的值。

**範例**

`print`運算子可用來快速評估一個或多個純量運算式，並將單一資料列資料表從產生的值中取出。
例如：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print 0 + 1 + 2 + 3 + 4 + 5, x = "Wow!"
```
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print banner=strcat("Hello", ", ", "World!")
```
