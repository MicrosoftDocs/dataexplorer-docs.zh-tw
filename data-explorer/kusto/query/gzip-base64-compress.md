---
title: gzip_compress_to_base64_string-Azure 資料總管
description: '本文描述 Azure 資料總管中 ( # A1 命令 gzip_compress_to_base64_string。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 11/01/2020
ms.openlocfilehash: 2d5d19a1a0d90a6bcff42247b85d01497afd0fac
ms.sourcegitcommit: 0e2fbc26738371489491a96924f25553a8050d51
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148527"
---
# <a name="gzip_compress_to_base64_string"></a>gzip_compress_to_base64_string ( # A1

執行 gzip 壓縮，並將結果編碼為 base64。


## <a name="syntax"></a>語法

`gzip_compress_to_base64_string("`*input_string* 」`)`

## <a name="arguments"></a>引數

*input_string* ：輸入 `string` 、要壓縮的字串和 base64 編碼。 函數會接受一個字串引數。

## <a name="returns"></a>傳回

* 傳回 `string` ，表示 gzip 壓縮和 base64 編碼的原始字串。 
* 如果壓縮或編碼失敗，則會傳回空的結果。

## <a name="example"></a>範例
```kusto
print res = gzip_compress_to_base64_string("1234567890qwertyuiop")
```

**輸出：** 
> |"eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGd0xvZwAzAG5JZA = = "|

## <a name="next-steps"></a>後續步驟

使用 [gzip_decompress_from_base64_string ( # B1 ](gzip-base64-decompress.md) 來取出原始未壓縮的字串。
