---
title: array_index_of() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的array_index_of()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: f68c9385c55cedb4491033d137af087dafd26aa0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518610"
---
# <a name="array_index_of"></a>array_index_of()

搜索陣列以搜尋指定的項,並返回其位置。

**語法**

`array_index_of(`*陣列*,*數值*`)`

**引數**

* *陣列*:要搜索的輸入陣列。
* *值*:要搜索的值。 該值應為長、整數、雙精度、日期時間、時間跨度、小數點、字串或 guid 的類型。

**傳回**

查找的零基索引位置。
如果在陣列中找不到值,則返回 -1。

**範例**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|結果|
|---|
|3|

**另請參閱**

如果只想檢查陣列中是否存在值,但對其位置不感興趣,則可以使用[set_has_element(arr,值)](sethaselementfunction.md) - 這將提高查詢的可讀性,但從性能上講,這兩個函數都是相同的。