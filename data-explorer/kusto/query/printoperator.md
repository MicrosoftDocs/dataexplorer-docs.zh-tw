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
ms.openlocfilehash: 9a5a780a6f7bdf277566d1c0421c5ca2a3a93602
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346060"
---
# <a name="print-operator"></a>print 運算子

輸出含有一或多個純量運算式的單一資料列。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

## <a name="syntax"></a>語法

`print`[*ColumnName* `=` ]*ScalarExpression* ['，' ...]

## <a name="arguments"></a>引數

* *ColumnName*：要指派給輸出單數資料行的選項名稱。
* *ScalarExpression*：要評估的純量運算式。

## <a name="returns"></a>傳回

單一資料行的單一資料列資料表，其單一資料格具有已評估之*ScalarExpression*的值。

## <a name="examples"></a>範例

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
