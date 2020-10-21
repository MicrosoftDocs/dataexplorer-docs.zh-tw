---
title: 'base64_decode_tostring ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 base64_decode_tostring。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: eab1065713bcbc73913a2ab17a99894d9af9f8a4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252708"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

將 base64 字串解碼為 UTF-8 字串。

## <a name="syntax"></a>語法

`base64_decode_tostring(`*字串*`)`

## <a name="arguments"></a>引數

* *字串*：要從 base64 解碼為 UTF8-8 字串的輸入字串。

## <a name="returns"></a>傳回

傳回從 base64 字串解碼的 UTF-8 字串。

* 若要將 base64 字串解碼為 long 值陣列，請參閱 [base64_decode_toarray ( # B1 ](base64_decode_toarrayfunction.md)
* 若要將字串解碼為 base64 字串，請參閱 [base64_encode_tostring ( # B1 ](base64_encode_tostringfunction.md)

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|Quine|
|-----|
|Kusto|

嘗試解碼從無效 UTF-8 編碼產生的 base64 字串將會傳回 null：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
