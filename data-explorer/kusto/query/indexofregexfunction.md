---
title: indexof_regex() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 indexof_regex()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6da0523e85bab4883c50708ffe3f7d087fdd8c8f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513884"
---
# <a name="indexof_regex"></a>indexof_regex()

函數報告輸入字串中第一次出現的指定字串的零基索引。 純字串匹配不會重疊。 

請參考[`indexof()`](indexoffunction.md)。

**語法**

`indexof_regex(`*源*`,`*查找*`[,`*occurrence*`[,``[,`start_index長度發生*length**start_index*`]]])`

**引數**

* *源*:輸入字串。  
* *尋找*:要查找的字串。
* *start_index*:搜索起始位置(可選)。
* *長度*:要檢查的字元位置數,-1 定義無限長度(可選)。
* *出現*:是發生預設值 1(可選)。

**傳回**

*查找*的零基索引位置。

如果在輸入中找不到字串,則返回 -1。
如果不相關(小於*0)start_index,**發生*或(小於 -1)*長度*參數 - 返回*空*。


**範例**
```kusto
print
 idx1 = indexof_regex("abcabc", "a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", "a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", "a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", "a.a", 0, -1, 2)  // Plain string matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", "a|ab", -1)  // invalid input
```

|idx1|idx2|idx3|idx4|idx5
|----|----|----|----|----
|0   |3   |-1  |-1  |    