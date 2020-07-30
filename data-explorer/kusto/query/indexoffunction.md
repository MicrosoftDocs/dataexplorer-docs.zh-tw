---
title: indexof （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 indexof （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8e237441d28f12ffc6f27f8a591980a701825e39
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347454"
---
# <a name="indexof"></a>indexof()

報告輸入字串內指定之字串第一次出現時的所在索引（以零為基底）。

如果查閱或輸入字串不是*字串*類型，函式會強制將值轉換成*字串*。

如需詳細資訊，請參閱 [`indexof_regex()`](indexofregexfunction.md)。

## <a name="syntax"></a>語法

`indexof(`*來源* `,`*查閱* `[,`*start_index* `[,`*長度* `[,`*發生次數*`]]])`

## <a name="arguments"></a>引數

* *來源*：輸入字串。  
* *查閱*：要查詢的字串。
* *start_index*：搜尋開始位置。 選擇性。
* *長度*：要檢查的字元位置數目。 -1 的值表示無限制的長度。 選擇性。
* *發生*次數：發生次數。 預設值：1。 選擇性。

## <a name="returns"></a>傳回

*Lookup*以零為起始的索引位置。

如果在輸入中找不到字串，則傳回-1。

如果不相關（小於0） *start_index*、*發生次數*或（小於-1）*長度*參數，則會傳回*null*。

## <a name="examples"></a>範例
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
