---
title: make_string （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 make_string （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 035be5910d173c75e585baa0b093dc6276bd4d63
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818592"
---
# <a name="make_string"></a>make_string()

傳回 Unicode 字元所產生的字串。
    
**語法**

`make_string (`*Arg1*[， *...argn*] .。。`)`

**引數**

* *Arg1* .。。*...Argn*：是整數（int 或 long）的運算式，或包含整數陣列的動態值。

* 此函式最多可接收64個引數。

**傳回**

傳回 Unicode 字元的字串，其值是由這個函數的引數所提供。 輸入必須包含有效的 Unicode 字碼指標。
如果有任何引數未對應至 Unicode 字元，則函式會傳回 `null` 。

**範例**

```kusto
print str = make_string(75, 117, 115, 116, 111)
```

|字串|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115, 116, 111]))
```

|字串|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115]), 116, 111)
```

|字串|
|---|
|Kusto|

```kusto
print str = make_string(75, 10, 117, 10, 115, 10, 116, 10, 111)
```

|字串|
|---|
|K<br>u<br>s<br>t<br>o|
