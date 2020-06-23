---
title: String 資料類型-Azure 資料總管
description: 本文說明 Azure 資料總管中的字串資料類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e7c043a9b4d8b141d2dc45e88022e191e6483c35
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264805"
---
# <a name="the-string-data-type"></a>String 資料類型

`string`資料類型代表 Unicode 字串。 Kusto 字串是以 UTF-8 編碼，且預設限制為1MB。

## <a name="string-literals"></a>字串常值

有數種方式可以編碼資料類型的常值 `string` 。

* 以雙引號括住字串（ `"` ）：`"This is a string literal. Single quote characters (') don't require escaping. Double quote characters (\") are escaped by a backslash (\\)"`
* 以單引號括住字串（ `'` ）：`'Another string literal. Single quote characters (\') require escaping by a backslash (\\). Double quote characters (") do not require escaping.'`

在上述兩個標記法中，反斜線（ `\` ）字元表示已進行轉義。
它是用來將括住的引號字元、定位字元（ `\t` ）、分行符號（） `\n` 和本身（）加以轉義 `\\` 。

同時也支援逐字字串常值。 在此表單中，反斜線字元（ `\` ）代表本身，而不是逸出字元。

* 以雙引號括住（ `"` ）：`@"This is a verbatim string literal that ends with a backslash\"`
* 以單引號括住（ `'` ）：`@'This is a verbatim string literal that ends with a backslash\'`

查詢中不含任何內容的兩個字串常值，或只以空白字元和批註分隔，會自動聯結以形成新的字串常值。
例如，下列運算式全部都會產生長度 `13` 。

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

如您所見，當字串以雙引號（）括住時 `"` ，單引號（ `'` ）字元並不需要進行轉義，還有另一種方法。 這個方法可讓您更輕鬆地根據內容來括住字串。

## <a name="obfuscated-string-literals"></a>模糊字串常值

系統會追蹤查詢，並將它們儲存以供遙測和分析之用。
例如，可能會讓叢集擁有者使用查詢文字。 如果查詢文字包含秘密資訊（例如密碼），則可能會洩漏應該保持私用的資訊。 為了避免發生這類流失問題，查詢作者可以將特定字串常值標示為**模糊字串常**值。
查詢文字中的這類常值會自動以星號（ `*` ）字元取代，因此無法用於稍後的分析。

> [!IMPORTANT]
> 將包含秘密資訊的所有字串常值標記為模糊字串常值。

您可以採用「一般」字串常值來形成模糊字串常值，並 `h` 在前面加上或 `H` 字元。 

例如：

```kusto
h'hello'
h@'world'
h"hello"
```

> [!NOTE]
> 在許多情況下，只有一部分的字串常值是秘密。 在這些情況下，請將常值分割成非秘密部分和秘密部分。 然後，只將秘密部分標示為模糊。

例如：

```kusto
print x="https://contoso.blob.core.windows.net/container/blob.txt?"
  h'sv=2012-02-12&se=2013-04-13T0...'
```
