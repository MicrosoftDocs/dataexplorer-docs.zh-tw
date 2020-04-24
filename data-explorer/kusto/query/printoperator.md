---
title: 列印運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的列印運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2019
ms.openlocfilehash: b751b07446a74ef17002abac26e4c881a2da757b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510960"
---
# <a name="print-operator"></a>print 運算子

使用一個或多個標量表達式輸出單行。

```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

**語法**

`print`【*欄位*`=`名稱 】*Scalar表示式*[', ' ...

**引數**

* *列名*:要分配給輸出的單數列的選項名稱。
* *標量運算式*:要計算的標量運算式。

**傳回**

儲存格表,單格的數字的值具有計算的*ScalarExpression*。

**範例**

運算元`print`可用作計算一個或多個標量運算式並從結果值中生成單行表的快速方法。
例如：

```kusto
print 0 + 1 + 2 + 3 + 4 + 5, x = "Wow!"
```

```kusto
print banner=strcat("Hello", ", ", "World!")
```