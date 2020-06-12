---
title: base64_decode_toarray （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 base64_decode_toarray （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: df10ea2bbdf4e48d32c087d35eb9361fc4d697b8
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717201"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

將 base64 字串解碼為 long 值的陣列。

**語法**

`base64_decode_toarray(`*字串*`)`

**引數**

* *String*：要從 base64 解碼成 UTF8 字串的輸入字串。

**傳回**

傳回從 base64 字串解碼的 long 值陣列。

* 若要將 base64 字串解碼為 UTF-8 字串，請參閱[base64_decode_tostring （）](base64_decode_tostringfunction.md)
* 若要將字串編碼為 base64 字串，請參閱[base64_encode_tostring （）](base64_encode_tostringfunction.md)

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|Quine|
|-----|
|[75117115116111]|

如果您嘗試將從無效 UTF-8 編碼所產生的 base64 字串解碼，則會傳回 "null"：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|空白|
|-----|
||
