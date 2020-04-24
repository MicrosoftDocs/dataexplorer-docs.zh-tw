---
title: 字串資料類型 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的字串數據類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c040045ba7146cf56487ca3a8729372084d3bf2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509617"
---
# <a name="the-string-data-type"></a>字串資料型態

資料類型`string`表示 Unicode 字串。 (Kusto 字串在 UTF-8 中編碼,默認情況下限制為 1MB。

## <a name="string-literals"></a>字串常值

有幾種方法可以對`string`資料類型的字面進行編碼:

* 以將字串括在雙引號中 ():`"``"This is a string literal. Single quote characters (') do not require escaping. Double quote characters (\") are escaped by a backslash (\\)"`
* 將字串括在單引號中 (`'`):`'Another string literal. Single quote characters (\') require escaping by a backslash (\\). Double quote characters (") do not require escaping.'`

在上面的兩個表示中,反斜杠`\`( ) 字元指示轉義。
它用於轉義包含引號字元、制表元 ()、`\t`換`\n`行元`\\`() 和自身 ()。

也支援逐字字串文本。 在此表單中,反斜槓字元`\`( ) 代表本身,而不是作為轉義字元:

* 以雙引號括起來`"`( ):`@"This is a verbatim string literal that ends with a backslash\"`
* 以單引括起來`'`( ):`@'This is a verbatim string literal that ends with a backslash\'`

查詢文本中的兩個字串文本之間沒有任何內容,或者僅由空格和註釋分隔,它們會自動串聯在一起形成新的字串文本(直到無法進行此類替換)。
例如,以下表示式所有產生`13`:

```kusto
print strlen("Hello"', '@"world!"); // Nothing between them

print strlen("Hello" ', ' @"world!"); // Separated by whitespace only

print strlen("Hello"
  // Comment
  ', '@"world!"); // Separated by whitespace and a comment
```

## <a name="examples"></a>範例

```kusto
// Simple string notation
print s1 = 'some string', s2 = "some other string"

// Strings that include single or double-quotes can be defined as follows
print s1 = 'string with " (double quotes)',
          s2 = "string with ' (single quotes)"

// Strings with '\' can be prefixed with '@' (as in c#)
print myPath1 = @'C:\Folder\filename.txt'

// Escaping using '\' notation
print s = '\\n.*(>|\'|=|\")[a-zA-Z0-9/+]{86}=='
```

可以看出,當字串以雙引號 ()`"`括起來時, 單引`'`號 ( ) 字元不需要轉義,反之亦然。 這樣,根據上下文對字串進行報價就更容易了。

## <a name="obfuscated-string-literals"></a>模糊字串文字

系統跟蹤查詢並將其存儲以用於遙測和分析目的。
例如,查詢文本可能提供給群集擁有者。 如果查詢文本包含機密資訊(如密碼),這可能會洩露應保密的資訊。 為了防止這種情況發生,查詢作者可能會將特定的字串文字標記為**模糊字串文字**。
查詢文字中的此類文字會自動替換為多個星形`*`( ) 字元,因此以後無法進行分析。

> [!IMPORTANT]
> **強烈建議將所有**包含機密資訊的字串文本標記為模糊字串文本。

模糊字串文字可以通過採用「一般」字串文本來形成,並預處理前面的字元`h`或`H`字元。 例如：

```kusto
h'hello'
h@'world'
h"hello"
```

> [!NOTE]
> 在許多情況下,只有字串文本的一部分是機密。 在這些情況下,將文本拆分為非機密部分和機密部分非常有用,然後只將機密部分標記為模糊部分。 例如：

```kusto
print x="https://contoso.blob.core.windows.net/container/blob.txt?"
  h'sv=2012-02-12&se=2013-04-13T0...'
```