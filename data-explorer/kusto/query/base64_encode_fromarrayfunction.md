---
title: 'base64_encode_fromarray ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 base64_encode_fromarray。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 88d9f5b26273fa91ae64ca637b8fe4e2650a8539
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252691"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

從位元組陣列編碼 base64 字串。

## <a name="syntax"></a>語法

`base64_encode_fromarray(`*BytesArray*`)`

## <a name="arguments"></a>引數

* *BytesArray*：要編碼成 base64 字串的輸入位元組陣列。

## <a name="returns"></a>傳回

傳回以位元組陣列編碼的 base64 字串。

* 若要將 base64 字串解碼為 UTF-8 字串，請參閱 [base64_decode_tostring ( # B1 ](base64_decode_tostringfunction.md)
* 如需將字串編碼為 base64 字串，請參閱 [base64_encode_tostring ( # B1 ](base64_encode_tostringfunction.md)
* 此函數是[base64_decode_toarray ( # B1](base64_decode_toarrayfunction.md)的反向

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let bytes_array = toscalar(print base64_decode_toarray("S3VzdG8="));
print decoded_base64_string = base64_encode_fromarray(bytes_array)
```

|decoded_base64_string|
|---|
|S3VzdG8 =|


嘗試從不正確位元組陣列（從不正確 UTF-8 編碼字串產生）將 base64 字串編碼，將會傳回 null：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let empty_bytes_array = toscalar(print base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA"));
print empty_string = base64_encode_fromarray(empty_bytes_array)
```

|empty_string|
|---|
||
