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
ms.openlocfilehash: 4cfe690b5ee2d32462552fb90300c9e3168b1f1d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349307"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

將 base64 字串解碼為 long 值的陣列。

## <a name="syntax"></a>語法

`base64_decode_toarray(`*字串*`)`

## <a name="arguments"></a>引數

* *String*：要從 base64 解碼成 UTF8 字串的輸入字串。

## <a name="returns"></a>傳回

傳回從 base64 字串解碼的 long 值陣列。

* 若要將 base64 字串解碼為 UTF-8 字串，請參閱[base64_decode_tostring （）](base64_decode_tostringfunction.md)
* 若要將字串編碼為 base64 字串，請參閱[base64_encode_tostring （）](base64_encode_tostringfunction.md)

## <a name="example"></a>範例

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
