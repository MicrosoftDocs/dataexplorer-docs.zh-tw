---
title: 'base64_decode_toarray ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 base64_decode_toarray。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 34a4601c35acb9c95e09e49d201b2e1cddaace8c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252729"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

將 base64 字串解碼為 long 值陣列。

## <a name="syntax"></a>語法

`base64_decode_toarray(`*字串*`)`

## <a name="arguments"></a>引數

* *字串*：要從 base64 解碼成 UTF8 字串的輸入字串。

## <a name="returns"></a>傳回

傳回從 base64 字串解碼的 long 值陣列。

* 若要將 base64 字串解碼為 UTF-8 字串，請參閱 [base64_decode_tostring ( # B1 ](base64_decode_tostringfunction.md)
* 若要將字串編碼為 base64 字串，請參閱 [base64_encode_tostring ( # B1 ](base64_encode_tostringfunction.md)

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|Quine|
|-----|
|[75117115116111]|

如果您嘗試將從無效 UTF-8 編碼產生的 base64 字串解碼，將會傳回 "null"：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
