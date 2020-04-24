---
title: url_encode_component() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的url_encode_component()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: bfdb40f362aa680a68bd8871769eecb5a2da19e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505061"
---
# <a name="url_encode_component"></a>url_encode_component()

該函數將輸入 URL 的字元轉換為可以通過 Internet 傳輸的格式。 

有關網址編碼和解碼的詳細資訊,請參閱[此處](https://en.wikipedia.org/wiki/Percent-encoding)。
與[url_encode](./urlencodefunction.md)不同,將空格編碼為"20%",而不是"+"。

**語法**

`url_encode_component(`*網址*`)`

**引數**

* *URL*:輸入 URL(字串)。  

**傳回**

URL(字串)轉換為可以通過 Internet 傳輸的格式。

**範例**

```kusto
let url = @'https://www.bing.com/hello word/';
print original = url, encoded = url_encode_component(url)
```

|原始|編碼|
|---|---|
|https://www.bing.com/hello字/|Htt%3a%2f%2fwww.bing.com%2fhello%20字|


 