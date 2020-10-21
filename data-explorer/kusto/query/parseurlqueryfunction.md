---
title: 'parse_urlquery ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 parse_urlquery。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b1092edfaca8c6789ec6c0dc478fb27505531b1e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244691"
---
# <a name="parse_urlquery"></a>parse_urlquery()

傳回 `dynamic` 包含查詢參數的物件。

## <a name="syntax"></a>語法

`parse_urlquery(`*查詢*`)`

## <a name="arguments"></a>引數

* *查詢*：字串代表 url 查詢。

## <a name="returns"></a>傳回

[動態](./scalar-data-types/dynamic.md)類型的物件，其中包含查詢參數。

## <a name="example"></a>範例

```kusto
parse_urlquery("k1=v1&k2=v2&k3=v3")
```

將會產生：

```kusto
 {
    "Query Parameters":"{"k1":"v1", "k2":"v2", "k3":"v3"}",
 }
```

**備註**

* 輸入格式應遵循 URL 查詢標準 (索引鍵 = 值& ... ) 
 