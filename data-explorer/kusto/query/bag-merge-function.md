---
title: bag_merge （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 bag_merge （）。
services: data-explorer
author: orspod
ms.author: orspod
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 06/18/2020
ms.openlocfilehash: 0a23f6ece8be3ba451c1f61a90eb65452b68f9ce
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265046"
---
# <a name="bag_merge"></a>bag_merge()

`dynamic`將屬性包合併到已 `dynamic` 合併所有屬性的屬性包中。

**語法**

`bag_merge(`*bag1* `, `*bag2* `[` ，` *bag3*, ...])`

**引數**

* *bag1 .。。bagN*：輸入 `dynamic` 屬性包。 函數接受2到64個引數。

**傳回**

傳回 `dynamic` 屬性包。 合併所有輸入屬性包物件的結果。 如果索引鍵出現在一個以上的輸入物件中，將會選擇任意值（超出此索引鍵的可能值）。

**範例**

運算式：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result = bag_merge(
   dynamic({'A1':12, 'B1':2, 'C1':3}),
   dynamic({'A2':81, 'B2':82, 'A1':1}))
```

|result|
|---|
|{<br>  "A1"：12，<br>  "B1"：2，<br>  "C1"：3，<br>  "A2"：81、<br>  "B2"：82<br>}|
