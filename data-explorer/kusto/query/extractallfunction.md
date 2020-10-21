---
title: 'extract_all ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 extract_all。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 85c118e8cd68c52278a34080eda4936151600cd5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247529"
---
# <a name="extract_all"></a>extract_all()

從文字字串取得 [正則運算式](./re2.md) 的所有符合專案。
（選擇性）取得相符群組的子集。

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") // results with the dynamic array ["123", "567", "789"]
```

## <a name="syntax"></a>語法

`extract_all(`*RegEx* `,`[*captureGroups* `,` ]*文字*`)`

## <a name="arguments"></a>引數

|引數        |描述                                  |必要或選擇性  |
|----------------|---------------------------------------------|----------------------|
|RegEx           | [正則運算式](./re2.md)。 運算式至少必須有一個捕獲群組，且小於或等於16個捕獲群組                                                         |必要              |
|captureGroups   |動態陣列常數，表示要解壓縮的捕獲群組。 有效的值從1到正則運算式中的捕獲群組數目。 也可以使用命名的 capture 群組 (請參閱 [範例](#examples)) |選擇性         |
|文字            |`string`要搜尋的                         |必要              |

## <a name="returns"></a>傳回

* 如果 *RegEx* 在 *文字*中找到相符專案：傳回動態陣列，包括針對指定的 [capture 群組 *captureGroups*] 的所有相符專案，或 *RegEx*中的所有捕獲群組。
* 如果 *captureGroups* 的數目是1：傳回的陣列具有相符值的單一維度。
* 如果*captureGroups*的數目超過1：傳回的陣列是每個*captureGroups*選取範圍的多重值符合的二維集合，或如果省略*captureGroups* ，則為*RegEx*中的所有 capture 群組。
* 如果沒有相符的內容： `null` 。

## <a name="examples"></a>範例

### <a name="extract-a-single-capture-group"></a>將單一 capture 群組解壓縮

傳回 GUID (兩個十六進位位數) 的十六進位位元組表示。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|識別碼|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["82"、"b8"、"是"、"2d"、"df"、"a7"、"4b"、"d1"、"8f"、"63"、"24"、"ad"、"26"、"d3"、"14"、"49"]|

### <a name="extract-several-capture-groups"></a>解壓縮數個 capture 群組 

使用具有三個捕獲群組的正則運算式，將每個 GUID 部分分割為第一個字母、最後一個字母，以及中間的任何內容。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id)
```

|識別碼|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8"、"2b8be2"、"d"]、["d"、"fa"、"7"]、["4"、"bd"、"1"]、["8"、"f6"、"3"]、["2"、"4ad26d3144"、"9"]]|

### <a name="extract-a-subset-of-capture-groups"></a>解壓縮 capture 群組的子集

顯示如何選取部分的捕獲群組。 正則運算式會比對第一個字母、最後一個字母和所有其餘部分。 *CaptureGroups*參數只會用來選取第一個和最後一個部分。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|識別碼|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8"、"d"]、["d"、"7"]、["4"、"1"]、["8"、"3"]、["2"、"9"]]|

### <a name="using-named-capture-groups"></a>使用已命名的捕獲群組

您可以在 extract_all ( # A1 中使用 RE2 的命名捕獲群組。
*CaptureGroups*會使用 capture 群組索引和名為 capture 群組參考來提取相符的值。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|識別碼|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8"、"2b8be2"、"d"]、["d"、"fa"、"7"]、["4"、"bd"、"1"]、["8"、"f6"、"3"]、["2"、"4ad26d3144"、"9"]]|
