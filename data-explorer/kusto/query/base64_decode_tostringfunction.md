---
title: base64_decode_tostring() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的base64_decode_tostring()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: a821fb07d62d5bca3982cc4c48b8e0457641f3f2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518168"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

將 base64 字串解碼為 UTF-8 字串

**語法**

`base64_decode_tostring(`*字串*`)`

**引數**

* *字串*:要從 base64 解碼到 UTF8-8 字串的輸入字串。

**傳回**

返回從 base64 字串解碼的 UTF-8 字串。

* 要將 base64 字串解碼為長值陣列,請參閱[base64_decode_toarray()](base64_decode_toarrayfunction.md)
* 有關將字串編碼到 base64 字串,請參閱[base64_encode_tostring()](base64_encode_tostringfunction.md)

**範例**

```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|奎因|
|-----|
|Kusto|

試著解碼從不合法的 UTF-8 編碼產生的 base64 字串將傳回 null:

```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|空白|
|-----|
||