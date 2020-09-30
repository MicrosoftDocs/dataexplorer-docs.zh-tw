---
title: zlib_compress_to_base64_string-Azure 資料總管
description: '本文描述 Azure 資料總管中 ( # A1 命令 zlib_compress_to_base64_string。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 09/29/2020
ms.openlocfilehash: c43ffcbb449c6fc8a4c01b7a032df50c40829702
ms.sourcegitcommit: 463ee13337ed6d6b4f21eaf93cf58885d04bccaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573505"
---
# <a name="zlib_compress_to_base64_string"></a>zlib_compress_to_base64_string ( # A1

執行 zlib 壓縮，並將結果編碼為 base64。

> [!NOTE]
> 唯一支援的 windows 大小為15。

## <a name="syntax"></a>語法

`zlib_compress_to_base64_string('input_string')`

## <a name="arguments"></a>引數

*input_string*：輸入 `string` 、要壓縮的字串和 base64 編碼。 函數會接受一個字串引數。

## <a name="returns"></a>傳回值

* 傳回 `string` ，代表 zlib 壓縮和 base64 編碼的原始字串。 
* 如果壓縮或編碼失敗，則會傳回空的結果。

## <a name="example"></a>範例

### <a name="using-kusto"></a>使用 Kusto

```kusto
print zcomp = zlib_compress_to_base64_string("1234567890qwertyuiop")
```

**輸出：** | "eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGdw = = "|

### <a name="using-python"></a>使用 Python

您可以使用其他工具（例如 Python）來完成壓縮： 

```python
print(base64.b64encode(zlib.compress(b'<original_string>')))
```

## <a name="next-steps"></a>後續步驟

使用 [zlib_decompress_from_base64_string ( # B1 ](zlib-base64-decompress.md) 來取出原始未壓縮的字串。