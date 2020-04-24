---
title: base64_encode_fromarray() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的base64_encode_fromarray()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: f601463fd6751be7064892e70e5b2235f96a20ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518066"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

從位元組對 base64 字串進行編碼。

**語法**

`base64_encode_fromarray(`*位元陣列*`)`

**引數**

* *位元組*:要編碼到基64字串的輸入位元組。

**傳回**

返回從位元組編碼的 base64 字串。

* 有關將基64字串解碼為 UTF-8 字串,請參閱[base64_decode_tostring()](base64_decode_tostringfunction.md)
* 有關將字串編碼到 base64 字串,請參閱[base64_encode_tostring()](base64_encode_tostringfunction.md)
* 此函數與[base64_decode_toarray()](base64_decode_toarrayfunction.md)相反

**範例**

```kusto
let bytes_array = toscalar(print base64_decode_toarray("S3VzdG8="));
print decoded_base64_string = base64_encode_fromarray(bytes_array)
```

|decoded_base64_string|
|---|
|S3VzdG8*|


試著從不合法的 UTF-8 編碼字串產生的無效位元組對 base64 字串進行編碼,將傳回 null:

```kusto
let empty_bytes_array = toscalar(print base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA"));
print empty_string = base64_encode_fromarray(empty_bytes_array)
```

|empty_string|
|---|
||