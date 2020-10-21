---
title: 'base64_encode_tostring ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 base64_encode_tostring。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 6fca71cbc7cf33f49a9f4698553391a84ddbb728
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252665"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

將字串編碼為 base64 字串。

## <a name="syntax"></a>語法

`base64_encode_tostring(`*字串*`)`

## <a name="arguments"></a>引數

* *字串*：要編碼為 base64 字串的輸入字串。

## <a name="returns"></a>傳回

傳回編碼為 base64 字串的字串。

* 若要將 base64 字串解碼為 UTF-8 字串，請參閱 [base64_decode_tostring ( # B1 ](base64_decode_tostringfunction.md)
* 若要將 base64 字串解碼為 long 值陣列，請參閱 [base64_decode_toarray ( # B1 ](base64_decode_toarrayfunction.md)


## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quine   |
|--------|
|S3VzdG8 =|

