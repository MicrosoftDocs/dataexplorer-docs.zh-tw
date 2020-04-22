---
title: parse_urlquery() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的parse_urlquery()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f00fefcd6245528d7ae50d6046d97289a92317d
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744607"
---
# <a name="parse_urlquery"></a>parse_urlquery()

返回包含`dynamic`查詢參數的物件。

**語法**

`parse_urlquery(`*查詢*`)`

**引數**

* *查詢*:字串表示 URL 查詢。

**傳回**

包含查詢參數的類型[動態](./scalar-data-types/dynamic.md)物件。

**範例**

```kusto
parse_urlquery("k1=v1&k2=v2&k3=v3")
```

將導致:

```kusto
 {
    "Query Parameters":"{"k1":"v1", "k2":"v2", "k3":"v3"}",
 }
```

**注意事項**

* 輸入格式應遵循網址 查詢標準(鍵=值& ...)
 