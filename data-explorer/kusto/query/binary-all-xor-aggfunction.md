---
title: binary_all_xor() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的binary_all_xor()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: a1908fe874576281c9ba45f23709845473b5725c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517692"
---
# <a name="binary_all_xor-aggregation-function"></a>binary_all_xor () (聚合函數)

使用每個匯總組的二進位`XOR`操作累積值(或者,如果總結在沒有分組的情況下完成)。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

總結`binary_all_xor(` *Expr*`)`

**引數**

* *Expr*: 長數。

**傳回**

返回使用二進位`XOR`操作對每個匯總組的記錄聚合的值(或者,如果匯總在沒有分組的情況下完成)。

**範例**

使用二進位`XOR`操作生產「咖啡食品」:

```kusto
datatable(num:long)
[
  0x44404440,
  0x1E1E1E1E,
  0x90ABBA09,
  0x000B105A,
]
| summarize result = toupper(tohex(binary_all_xor(num)))
```

|result|
|---|
|CAFEF00D|