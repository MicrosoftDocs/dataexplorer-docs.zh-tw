---
title: base64_encode_tostring （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 base64_encode_tostring （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 218f87a3c11695c4aa8135f98b0c445580de9ad0
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349256"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

將字串編碼為 base64 字串。

## <a name="syntax"></a>語法

`base64_encode_tostring(`*字串*`)`

## <a name="arguments"></a>引數

* *String*：要編碼為 base64 字串的輸入字串。

## <a name="returns"></a>傳回

傳回編碼為 base64 字串的字串。

* 若要將 base64 字串解碼為 UTF-8 字串，請參閱[base64_decode_tostring （）](base64_decode_tostringfunction.md)
* 若要將 base64 字串解碼為 long 值陣列，請參閱[base64_decode_toarray （）](base64_decode_toarrayfunction.md)


## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quine   |
|--------|
|S3VzdG8 =|

