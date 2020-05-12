---
title: base64_decode_tostring （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 base64_decode_tostring （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: d2e1c5dbb845e67a5306ccb16c234de383d3ca69
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225338"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

將 base64 字串解碼為 UTF-8 字串。

**語法**

`base64_decode_tostring(`*字串*`)`

**引數**

* *String*：要從 base64 解碼成 UTF8-8 字串的輸入字串。

**傳回**

傳回從 base64 字串解碼的 UTF-8 字串。

* 若要將 base64 字串解碼為 long 值陣列，請參閱[base64_decode_toarray （）](base64_decode_toarrayfunction.md)
* 如需將字串編碼為 base64 字串，請參閱[base64_encode_tostring （）](base64_encode_tostringfunction.md)

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|Quine|
|-----|
|Kusto|

嘗試將從無效 UTF-8 編碼所產生的 base64 字串解碼，將會傳回 null：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|空白|
|-----|
||
