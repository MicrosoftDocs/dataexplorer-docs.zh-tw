---
title: 'indexof_RegEx ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 indexof_RegEx。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5826920a411235166002b6833ed88869fb85f211
ms.sourcegitcommit: 88f8ad67711a4f614d65d745af699d013d01af32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638966"
---
# <a name="indexof_regex"></a>indexof_regex()

傳回在輸入字串內，第一次出現指定查閱正則運算式之以零為起始的索引。

請參閱 [`indexof()`](indexoffunction.md)。

## <a name="syntax"></a>語法

`indexof_regex(`*來源* `,`*查閱* `[,`*start_index* `[,`*長度* `[,`*發生次數*`]]])`

## <a name="arguments"></a>引數

|引數     | 描述                                     |必要或選擇性|
|--------------|-------------------------------------------------|--------------------|
|source        | 輸入字串                                    |必要            |
|lookup        | 正則運算式查閱字串。               |必要            |
|start_index   | 搜尋開始位置                           |選擇性            |
|長度        | 要檢查的字元位置數目。 -1 定義無限制的長度 |選擇性            |
|occurrence    | 尋找模式的第 N 個外觀索引。 
                 預設值為1，第一次出現的索引 |選擇性            |

## <a name="returns"></a>傳回

*Lookup* 以零為起始的索引位置。

* 如果在輸入中找不到字串，則傳回-1。
* 如果有下列情況，則傳回 *null* ：
     * start_index 小於0。
     * 發生次數小於0。
     * 長度參數小於-1。

> [!NOTE]
- 不支援重迭的相符專案查閱。
- 正則運算式字串可能包含需要以轉義或使用 @ ' ' 字串常值的字元。

## <a name="examples"></a>範例

```kusto
print
 idx1 = indexof_regex("abcabc", @"a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", @"a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", @"a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", @"a.a", 0, -1, 2)  // Matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", @"a|ab", -1)  // invalid start_index argument
```

|idx1|idx2|idx3|idx4|idx5|
|----|----|----|----|----|
|0   |3   |-1  |-1  |    |
