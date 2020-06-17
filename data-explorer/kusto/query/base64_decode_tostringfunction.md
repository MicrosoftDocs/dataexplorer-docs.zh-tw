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
ms.openlocfilehash: ffef2fd609075b0d5e5af5c4064e079c27cd8c94
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818511"
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
* 若要將字串解碼為 base64 字串，請參閱[base64_encode_tostring （）](base64_encode_tostringfunction.md)

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
