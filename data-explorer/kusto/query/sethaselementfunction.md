---
title: set_has_element() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 azure 數據資源管理器中的 set_has_element()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 256e01646c6ecd39a8a589299acd6620008ece28
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507764"
---
# <a name="set_has_element"></a>set_has_element()

確定指定的集是否包含指定的元素。

**語法**

`set_has_element(`*陣列*,*數值*`)`

**引數**

* *陣列*:要搜索的輸入陣列。
* *值*:要搜索的值。 `long`該值應為`integer`類型`double``datetime`、 `timespan` `decimal` `string`、 `guid`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、

**傳回**

根據陣列中是否存在值,則為真或假。

**範例**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|結果|
|---|
|1|

**另請參閱**

如果您還對陣列中存在的值的位置感興趣,則可以使用[array_index_of(arr,值)。](arrayindexoffunction.md) 從性能上講,這兩個函數都是相同的。