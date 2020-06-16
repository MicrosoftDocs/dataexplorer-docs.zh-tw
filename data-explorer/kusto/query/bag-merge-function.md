---
title: bag_merge （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 bag_merge （）。
services: data-explorer
author: elgevork
ms.author: elgevork
ms.reviewer: ''
ms.service: data-explorer
ms.topic: reference
ms.date: 06/14/2020
ms.openlocfilehash: f5ca4888b0c3aad976d78c10bbbfa0810483c75b
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784594"
---
# <a name="bag_merge"></a>bag_merge()

將輸入動態屬性包物件合併到動態屬性包物件中。

**語法**

`bag_merge(`*動態物件* `,`*動態物件*`)`

**引數**

* 輸入以逗號分隔的動態物件。 函數支援2到64的輸入動態物件。

**傳回**

傳回 `dynamic` 屬性包。 合併所有輸入屬性包物件的結果。
如果索引鍵出現在一個以上的輸入物件中，將會選擇任意值（超出此索引鍵的可能值）。

**範例**

運算式：

`print result = bag_merge(dynamic({ 'A1':12, 'B1':2, 'C1':3}), dynamic({ 'A2':81, 'B2':82, 'A1':1}))`

評估為：

`{
  "A1": 12,
  "B1": 2,
  "C1": 3,
  "A2": 81,
  "B2": 82
}`
