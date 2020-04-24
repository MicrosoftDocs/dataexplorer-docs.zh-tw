---
title: base64_decode_toarray() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 base64_decode_toarray()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 80a702f112fd4d7b88b4011b3f615cf34acff62c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518185"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

將 base64 字串解碼為長值陣列。

**語法**

`base64_decode_toarray(`*字串*`)`

**引數**

* *字串*:要從 base64 解碼到 UTF8-8 字串的輸入字串。

**傳回**

返回從 base64 字串中編碼的長值陣組。

* 有關將基64字串解碼為 UTF-8 字串,請參閱[base64_decode_tostring()](base64_decode_tostringfunction.md)
* 有關將字串編碼到 base64 字串,請參閱[base64_encode_tostring()](base64_encode_tostringfunction.md)

**範例**

```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|奎因|
|-----|
|[75,117,115,116,111]|

試著解碼從不合法的 UTF-8 編碼產生的 base64 字串將傳回 null:

```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|空白|
|-----|
||