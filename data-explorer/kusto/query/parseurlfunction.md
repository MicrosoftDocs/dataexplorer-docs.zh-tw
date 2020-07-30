---
title: parse_url （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 parse_url （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 94a35dbf742b6df31012e68b5f2b2f09bec9b7e5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346281"
---
# <a name="parse_url"></a>parse_url()

剖析絕對 URL `string` ，並傳回 `dynamic` 包含 URL 部分的物件。


## <a name="syntax"></a>語法

`parse_url(`*連結*`)`

## <a name="arguments"></a>引數

* *url*：字串代表 URL 或 url 的查詢部分。

## <a name="returns"></a>傳回

[動態](./scalar-data-types/dynamic.md)類型的物件，其中包含 URL 元件：配置、主機、埠、路徑、使用者名稱、密碼、查詢參數、片段。

## <a name="example"></a>範例

```kusto
T | extend Result = parse_url("scheme://username:password@host:1234/this/is/a/path?k1=v1&k2=v2#fragment")
```

將會產生

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