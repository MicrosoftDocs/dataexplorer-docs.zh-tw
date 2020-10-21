---
title: 'indexof ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 indexof ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1558e2463c2958965fcb501aff99c7ec14fe8688
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252948"
---
# <a name="indexof"></a>indexof()

報告輸入字串內第一次出現的指定字串之以零為基底的索引。

如果查閱或輸入字串不是 *字串* 類型，則函式會強制將值轉換成 *字串*。

如需詳細資訊，請參閱 [`indexof_regex()`](indexofregexfunction.md)。

## <a name="syntax"></a>語法

`indexof(`*來源* `,`*查閱* `[,`*start_index* `[,`*長度* `[,`*發生次數*`]]])`

## <a name="arguments"></a>引數

* *來源*：輸入字串。  
* *lookup*：要查詢的字串。
* *start_index*：搜尋開始位置。 選擇性。
* *長度*：要檢查的字元位置數目。 -1 的值表示不限長度。 選擇性。
* *發生*次數：出現次數。 預設值為1。 選擇性。

## <a name="returns"></a>傳回

*Lookup*以零為起始的索引位置。

如果在輸入中找不到字串，則傳回-1。

如果不相關 (小於 0) *start_index*、 *出現*或 (小於-1) *長度* 參數，則傳回 *null*。

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
