---
title: url_decode() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 url_decode()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3ffa5052a2fc30387be118683ec1df6f34f7346f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505146"
---
# <a name="url_decode"></a>url_decode()

該函數將編碼的 URL 轉換為常規 URL 表示形式。 

有關網址解碼和編碼的詳細資訊,請參閱[此處](https://en.wikipedia.org/wiki/Percent-encoding)。

**語法**

`url_decode(`*編碼網址*`)`

**引數**

* *編碼網址*:編碼網址(字串)。  

**傳回**

常規表示中的網址(字串)。

**範例**

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|原始|解碼|
|---|---|
|Ht%3a%2f%2fwww.bing.com%2f|https://www.bing.com/|



 