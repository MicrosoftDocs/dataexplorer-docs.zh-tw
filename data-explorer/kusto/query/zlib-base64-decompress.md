---
title: 'zlib_decompress_from_base64_string ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 命令 zlib_decompress_from_base64_string。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 09/29/2020
ms.openlocfilehash: e2163b3fe5460a660579889a589acd1e5fd965cd
ms.sourcegitcommit: 463ee13337ed6d6b4f21eaf93cf58885d04bccaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573502"
---
# <a name="zlib_decompress_from_base64_string"></a>zlib_decompress_from_base64_string ( # A1

從 base64 解碼輸入字串，並執行 zlib 解壓縮。

> [!NOTE]
> 唯一支援的 windows 大小為15。

## <a name="syntax"></a>語法

`zlib_decompress_from_base64_string('input_string')`

## <a name="arguments"></a>引數

*input_string*： `string` 以 zlib 壓縮然後以 base64 編碼的輸入。 函數會接受一個字串引數。

## <a name="returns"></a>傳回值

* 傳回 `string` 表示原始字串的。 
* 如果解壓縮或解碼失敗，則會傳回空的結果。 
    * 例如，不正確 zlib 壓縮和 base 64 編碼的字串會傳回空的輸出。

## <a name="examples"></a>範例

```kusto
print zcomp = zlib_decompress_from_base64_string("eJwLSS0uUSguKcrMS1cwNDIGACxqBQ4=")
```

**輸出：**

|測試字串 123 |

無效輸入的範例：

```kusto
print zcomp = zlib_decompress_from_base64_string("x0x0x0")
```

**輸出：**
||

## <a name="next-steps"></a>後續步驟

使用 [zlib_compress_to_base64_string ( # B1 ](zlib-base64-compress.md)建立壓縮的輸入字串。