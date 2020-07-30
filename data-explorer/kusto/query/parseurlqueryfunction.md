---
title: parse_urlquery （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 parse_urlquery （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d34ece3a945485b8a809089d030fa954b070a28
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346264"
---
# <a name="parse_urlquery"></a>parse_urlquery()

傳回 `dynamic` 包含查詢參數的物件。

## <a name="syntax"></a>語法

`parse_urlquery(`*查詢*`)`

## <a name="arguments"></a>引數

* *query*：表示 url 查詢的字串。

## <a name="returns"></a>傳回

[動態](./scalar-data-types/dynamic.md)類型的物件，其中包含查詢參數。

## <a name="example"></a>範例

```kusto
parse_urlquery("k1=v1&k2=v2&k3=v3")
```

會產生：

```kusto
 {
    "Query Parameters":"{"k1":"v1", "k2":"v2", "k3":"v3"}",
 }
```

**備註**

* 輸入格式應遵循 URL 查詢標準（索引鍵 = 值& ...）
 