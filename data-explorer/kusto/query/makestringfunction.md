---
title: make_string() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的make_string()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5af7cab9106088064048c1077ec3f9b1950ec08
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512626"
---
# <a name="make_string"></a>make_string()

返回 Unicode 字元產生的字串。
    
**語法**

`make_string (`*Arg1* [, *ArgN*]...`)`

**引數**

* *Arg1* ...*ArgN* :整數(int 或 long)或包含積分陣列的動態值的運算式。

* 此函數最多接收 64 個參數。 

**傳回**

返回由 Unicode 字元組成的字串,其代碼點值由此函數的參數提供。 輸入必須包含有效的 Unicode 代碼點。
如果任何參數未映射到 Unicode 字元,則函數傳回 null。

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