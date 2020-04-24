---
title: to_utf8() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 to_utf8()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9d48ed99e517e0b1e5d498e80deaa48dc1cd3601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505826"
---
# <a name="to_utf8"></a>to_utf8()

返回輸入字串的單碼字元的動態陣列(make_string的反向操作)。

**語法**

`to_utf8(`*源*`)`

**引數**

* *源*:要轉換的源字串。

**傳回**

返回構成提供給此函數的字串的單碼字元的動態數位。
參見[`make_string()`](makestringfunction.md))

**範例**

```kusto
print arr = to_utf8("⒦⒰⒮⒯⒪")
```

|阿爾爾|
|---|
|[9382, 9392, 9390, 9391, 9386]|

```kusto
print arr = to_utf8("קוסטו - Kusto")
```

|阿爾爾|
|---|
|[1511, 1493, 1505, 1496, 1493, 32, 45, 32, 75, 117, 115, 116, 111]|

```kusto
print str = make_string(to_utf8("Kusto"))
```

|字串|
|---|
|Kusto|
