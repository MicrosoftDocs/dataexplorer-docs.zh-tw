---
title: base64_encode_fromarray （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 base64_encode_fromarray （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: ede35223b9cc57fddc5318659e6902130a80c011
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349273"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

從位元組陣列將 base64 字串編碼。

## <a name="syntax"></a>語法

`base64_encode_fromarray(`*BytesArray*`)`

## <a name="arguments"></a>引數

* *BytesArray*：要編碼為 base64 字串的輸入位元組陣列。

## <a name="returns"></a>傳回

傳回以位元組陣列編碼的 base64 字串。

* 如需將 base64 字串解碼為 UTF-8 字串，請參閱[base64_decode_tostring （）](base64_decode_tostringfunction.md)
* 如需將字串編碼為 base64 字串，請參閱[base64_encode_tostring （）](base64_encode_tostringfunction.md)
* 此函式是 base64_decode_toarray 的反向[（）](base64_decode_toarrayfunction.md)

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let bytes_array = toscalar(print base64_decode_toarray("S3VzdG8="));
print decoded_base64_string = base64_encode_fromarray(bytes_array)
```

|decoded_base64_string|
|---|
|S3VzdG8 =|


嘗試從不正確 UTF-8 編碼字串產生的無效位元組陣列編碼 base64 字串時，將會傳回 null：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let empty_bytes_array = toscalar(print base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA"));
print empty_string = base64_encode_fromarray(empty_bytes_array)
```

|empty_string|
|---|
||
