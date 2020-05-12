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
ms.openlocfilehash: 332ff6bedd268dd79be020ff1dc4d0591ed486f7
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225304"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

將字串編碼為 base64 字串。

**語法**

`base64_encode_tostring(`*字串*`)`

**引數**

* *String*：要編碼為 base64 字串的輸入字串。

**傳回**

傳回編碼為 base64 字串的字串。

* 如需將 base64 字串解碼為 UTF-8 字串，請參閱[base64_decode_tostring （）](base64_decode_tostringfunction.md)
* 若要將 base64 字串解碼為 long 值陣列，請參閱[base64_decode_toarray （）](base64_decode_toarrayfunction.md)


**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quine   |
|--------|
|S3VzdG8 =|
