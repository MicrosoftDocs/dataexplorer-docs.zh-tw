---
title: 列印操作員-Azure 資料總管
description: 本文說明 Azure 資料總管中的列印操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2019
ms.openlocfilehash: 19fa7a22a4f26d7d66a6224b4943f7ed976b531f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249583"
---
# <a name="print-operator"></a>print 運算子

輸出具有一或多個純量運算式的單一資料列。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

## <a name="syntax"></a>語法

`print` [*ColumnName* `=` ] *ScalarExpression* ['，' ...]

## <a name="arguments"></a>引數

* *ColumnName*：要指派給輸出單一資料行的選項名稱。
* *ScalarExpression*：要評估的純量運算式。

## <a name="returns"></a>傳回

單一資料行的單一資料列資料表，其中的單一資料格具有評估 *ScalarExpression*的值。

## <a name="examples"></a>範例

`print`運算子可作為快速評估一或多個純量運算式，並將單一資料列資料表從結果值中取出的方法。
例如：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print 0 + 1 + 2 + 3 + 4 + 5, x = "Wow!"
```
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print banner=strcat("Hello", ", ", "World!")
```
