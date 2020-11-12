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
ms.openlocfilehash: a35fd4ed2f43c991aa08e2e1594103cb5f5bd7a7
ms.sourcegitcommit: 3eabd78305d32cd9b8a6bd1d76877ddc19d8ac63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548881"
---
# <a name="gzip_decompress_from_base64_string"></a>gzip_decompress_from_base64_string()

從 base64 解碼輸入字串，並執行 gzip 解壓縮。

## <a name="syntax"></a>語法

`gzip_decompress_from_base64_string("`*input_string*`")`

## <a name="arguments"></a>引數

*input_string* ： `string` 使用 gzip 壓縮然後以 base64 編碼的輸入。 函數會接受一個字串引數。

> [!NOTE]
> 此函式會檢查必要的 gzip 標頭欄位 (ID1、ID2 和 CM) ，如果其中有任何欄位有不正確的值，則會傳回空的輸出。
> 不支援選擇性的標頭欄位。 FLG 和 XFL 都應為零。


## <a name="returns"></a>傳回

* 傳回 `string` 表示原始字串的。 
* 如果解壓縮或解碼失敗，則會傳回空的結果。 
    * 例如，不正確 gzip 壓縮和 base 64 編碼的字串將會傳回空的輸出。

## <a name="examples"></a>範例

```kusto
print res=gzip_decompress_from_base64_string("H4sIAAAAAAAA/wEUAOv/MTIzNDU2Nzg5MHF3ZXJ0eXVpb3A6m7f2FAAAAA==")
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

* 使用 [gzip_compress_to_base64_string ( # B1 ](gzip-base64-compress.md)建立壓縮的輸入字串。
* 另請參閱 [zlib_decompress_from_base64_string](zlib-base64-decompress.md)。
