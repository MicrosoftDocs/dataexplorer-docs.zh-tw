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
ms.openlocfilehash: c414f1bdb83850bc6ec6065314bc7c8662ab0ed2
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717082"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

將字串編碼為 base64 字串。

**語法**

`base64_encode_tostring(`*字串*`)`

**引數**

* *String*：要編碼為 base64 字串的輸入字串。

**傳回**

傳回編碼為 base64 字串的字串。

* 若要將 base64 字串解碼為 UTF-8 字串，請參閱[base64_decode_tostring （）](base64_decode_tostringfunction.md)
* 若要將 base64 字串解碼為 long 值陣列，請參閱[base64_decode_toarray （）](base64_decode_toarrayfunction.md)


**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quine   |
|--------|
|S3VzdG8 =|

