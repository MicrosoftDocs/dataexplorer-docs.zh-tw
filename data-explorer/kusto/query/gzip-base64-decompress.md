---
title: 'gzip_decompress_from_base64_string ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 命令 gzip_decompress_from_base64_string。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 11/01/2020
ms.openlocfilehash: 19fc8c7ce74cfe632034722fda2b23c5105d013a
ms.sourcegitcommit: 0e2fbc26738371489491a96924f25553a8050d51
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148524"
---
# <a name="gzip_decompress_from_base64_string"></a>gzip_decompress_from_base64_string ( # A1

從 base64 解碼輸入字串，並執行 gzip 解壓縮。

## <a name="syntax"></a>語法

`gzip_decompress_from_base64_string("`*input_string*`")`

## <a name="arguments"></a>引數

*input_string* ： `string` 使用 gzip 壓縮然後以 base64 編碼的輸入。 函數會接受一個字串引數。

## <a name="returns"></a>傳回

* 傳回 `string` 表示原始字串的。 
* 如果解壓縮或解碼失敗，則會傳回空的結果。 
    * 例如，不正確 gzip 壓縮和 base 64 編碼的字串將會傳回空的輸出。

## <a name="examples"></a>範例

```kusto
print res=gzip_decompress_from_base64_string("eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGd0xvZwAzAG5JZA==")
```

**輸出：**

|"1234567890qwertyuiop "|

無效輸入的範例：

```kusto
print res=gzip_decompress_from_base64_string("x0x0x0")
```

**輸出：**
>||

## <a name="next-steps"></a>後續步驟

使用 [gzip_compress_to_base64_string ( # B1 ](gzip-base64-compress.md)建立壓縮的輸入字串。
