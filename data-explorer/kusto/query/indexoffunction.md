---
title: 索引() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的索引。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 698cc7c13c3d665f9f5cfe25a31269dc763c51fb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513935"
---
# <a name="indexof"></a>indexof()

函數報告輸入字串中第一次出現的指定字串的零基索引。

如果尋找或輸入字串不是字串型態 - 強制將值強制轉換為字串。

請參考[`indexof_regex()`](indexofregexfunction.md)。

**語法**

`indexof(`*源*`,`*查找*`[,`*occurrence*`[,``[,`start_index長度發生*length**start_index*`]]])`

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
 idx1 = indexof("abcdefg","cde")    // lookup found in input string
 , idx2 = indexof("abcdefg","cde",1,4) // lookup found in researched range 
 , idx3 = indexof("abcdefg","cde",1,2) // search starts from index 1, but stops after 2 chars, so full lookup can't be found
 , idx4 = indexof("abcdefg","cde",3,4) // search starts after occurrence of lookup
 , idx5 = indexof("abcdefg","cde",-1)  // invalid input
 , idx6 = indexof(1234567,5,1,4)       // two first parameters were forcibly casted to strings "12345" and "5"
 , idx7 = indexof("abcdefg","cde",2,-1)  // lookup found in input string
 , idx8 = indexof("abcdefgabcdefg", "cde", 1, 10, 2)   // lookup found in input range
 , idx9 = indexof("abcdefgabcdefg", "cde", 1, -1, 3)   // the third occurrence of lookup is not in researched range
```

|idx1|idx2|idx3|idx4|idx5|idx6|idx7|idx8|idx9|
|----|----|----|----|----|----|----|----|----|
|2   |2   |-1  |-1  |    |4   |2   |9   |-1  |
