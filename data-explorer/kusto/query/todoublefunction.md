---
title: 雙精度/到實() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的雙精度()/對實()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: eb9c976f1646f71fcf8b345899037461f58f4ef0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506319"
---
# <a name="todoubletoreal"></a>雙倍()/至實()

將輸入轉換為類型的`real`值。 (`todouble()``toreal()`是 同義詞。

```kusto
toreal("123.4") == 123.4
```

**語法**

`toreal(`*Expr*`)`
`todouble(`*Expr*`)`

**引數**

* *Expr:* 其值將轉換為類型`real`的 值的運算式。

**傳回**

如果轉換成功,則結果是類型`real`的值。
如果轉換不成功,則結果為值`real(null)`。

*注意*:如果可能[,最好使用雙()或真()。](./scalar-data-types/real.md)