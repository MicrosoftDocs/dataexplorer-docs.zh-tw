---
title: extract_all （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 extract_all （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 55168d381ab69bf0d29e8560714e13cb635fd688
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780519"
---
# <a name="extract_all"></a>extract_all()

從文字字串取得[正則運算式](./re2.md)的所有相符專案。
（選擇性）取得相符群組的子集。

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") // results with the dynamic array ["123", "567", "789"]
```

**語法**

`extract_all(`*RegEx* `,`[*captureGroups* `,` ]*文字*`)`

**引數**

|引數        |描述                                  |Required 或 Optional  |
|----------------|---------------------------------------------|----------------------|
|RegEx           | [正則運算式](./re2.md)。 運算式必須至少有一個「捕獲群組」和「小於或等於16個」捕獲群組                                                         |必要              |
|captureGroups   |動態陣列常數，表示要解壓縮的 capture 群組。 有效值是從1到正則運算式中的捕捉群組數目。 也允許命名的 capture 群組（請參閱[範例](#examples)）|選用         |
|text            |`string`要搜尋的                         |必要              |

**傳回**

* 如果*RegEx*在*文字*中找到相符專案：會傳回動態陣列，包括對指定的 capture 群組*captureGroups*的所有相符專案，或*RegEx*中的所有捕捉群組。
* 如果*captureGroups*數目為1：傳回的陣列具有符合值的單一維度。
* 如果*captureGroups*數目大於1：傳回的陣列是每個*captureGroups*選取範圍之多重值比對的二維集合，或如果省略*captureGroups*則為*RegEx*中出現的所有 capture 群組。
* 如果沒有相符的： `null` 。

## <a name="examples"></a>範例

### <a name="extract-a-single-capture-group"></a>將單一 capture 群組解壓縮

傳回 GUID 的十六進位位元組表示（兩個十六進位數位）。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|ID|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["82"，"b8"，"是"，"2d"，"df"，"a7"，"4b"，"d1"，"8f"，"63"，"24"，"ad"，"26"，"d3"，"14"，"49"]|

### <a name="extract-several-capture-groups"></a>解壓縮數個捕捉群組 

使用具有三個捕捉群組的正則運算式，將每個 GUID 部分分割成第一個字母、最後一個字母，以及中間的任何一個。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id)
```

|ID|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8"，"2b8be2"，"d"]，["d"，"fa"，"7"]，["4"，"bd"，"1"]，["8"，"f6"，"3"]，["2"，"4ad26d3144"，"9"]]|

### <a name="extract-a-subset-of-capture-groups"></a>解壓縮 capture 群組的子集

顯示如何選取捕捉群組的子集。 正則運算式會比對第一個字母、最後一個字母和所有其餘部分。 *CaptureGroups*參數是用來只選取第一個和最後一個部分。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|ID|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8"，"d"]，["d"，"7"]，["4"，"1"]，["8"，"3"]，["2"，"9"]]|

### <a name="using-named-capture-groups"></a>使用已命名的 capture 群組

您可以在 extract_all （）中使用已命名的 capture RE2 群組。
*CaptureGroups*會使用 capture 群組索引和命名的 capture group reference 來提取相符的值。

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|ID|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8"，"2b8be2"，"d"]，["d"，"fa"，"7"]，["4"，"bd"，"1"]，["8"，"f6"，"3"]，["2"，"4ad26d3144"，"9"]]|
