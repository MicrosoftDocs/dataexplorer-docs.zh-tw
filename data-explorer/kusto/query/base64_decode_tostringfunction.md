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
ms.openlocfilehash: 17d88c8be518b6d31b67a327a7a5d42818132cb9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349290"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

將 base64 字串解碼為 UTF-8 字串。

## <a name="syntax"></a>語法

`base64_decode_tostring(`*字串*`)`

## <a name="arguments"></a>引數

* *String*：要從 base64 解碼成 UTF8-8 字串的輸入字串。

## <a name="returns"></a>傳回

傳回從 base64 字串解碼的 UTF-8 字串。

* 若要將 base64 字串解碼為 long 值陣列，請參閱[base64_decode_toarray （）](base64_decode_toarrayfunction.md)
* 若要將字串解碼為 base64 字串，請參閱[base64_encode_tostring （）](base64_encode_tostringfunction.md)

## <a name="example"></a>範例

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
