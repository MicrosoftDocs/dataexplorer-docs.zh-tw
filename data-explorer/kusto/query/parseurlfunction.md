---
title: parse_url() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的parse_url()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dfc093964ce5b91acc01f798f8f62651266ab153
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511487"
---
# <a name="parse_url"></a>parse_url()

分析絕對`string`URL 並`dynamic`傳回包含 URL 部分的物件。


**語法**

`parse_url(`*網址*`)`

**引數**

* *網址*: 字串表示網址 或網址的查詢部分。

**傳回**

包含 URL 元件的類型[動態](./scalar-data-types/dynamic.md)物件:方案、主機、埠、路徑、使用者名、密碼、查詢參數、片段。

**範例**

```kusto
T | extend Result = parse_url("scheme://username:password@host:1234/this/is/a/path?k1=v1&k2=v2#fragment")
```

將導致

```
 {
    "Scheme":"scheme",
    "Host":"host",
    "Port":"1234",
    "Path":"this/is/a/path",
    "Username":"username",
    "Password":"password",
    "Query Parameters":"{"k1":"v1", "k2":"v2"}",
    "Fragment":"fragment"
 }
```