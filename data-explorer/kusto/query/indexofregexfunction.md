---
title: indexof_RegEx （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 indexof_RegEx （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 72797b54c3ba431b4a846f9e9661e9693359cceb
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780451"
---
# <a name="indexof_regex"></a>indexof_regex()

函式會報告輸入字串內指定之字串第一次出現時的所在索引（以零為基底）。 純字串相符專案不會重迭。

請參閱 [`indexof()`](indexoffunction.md)。

**語法**

`indexof_regex(`*來源* `,`*查閱* `[,`*start_index* `[,`*長度* `[,`*發生次數*`]]])`

**引數**

|引數     | 描述                                     |Required 或 Optional|
|--------------|-------------------------------------------------|--------------------|
|source        | 輸入字串                                    |必要            |
|lookup        | 要搜尋的字串                                  |必要            |
|start_index   | 搜尋開始位置                           |選用            |
|長度        | 要檢查的字元位置數目。 -1 定義無限長度 |選用            |
|occurrence    | 尋找模式的第 N 個外觀索引。 
                 預設值為1，第一次出現的索引 |選用            |

**傳回**

*Lookup*以零為起始的索引位置。

* 如果在輸入中找不到字串，則傳回-1。
* 如果是，則傳回*null* ：
     * start_index 小於0。
     * 發生次數小於0。
     * 長度參數小於-1。


**範例**

```kusto
print
 idx1 = indexof_regex("abcabc", "a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", "a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", "a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", "a.a", 0, -1, 2)  // Plain string matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", "a|ab", -1)  // invalid input
```

|idx1|idx2|idx3|idx4|idx5|
|----|----|----|----|----|
|0   |3   |-1  |-1  |    |
