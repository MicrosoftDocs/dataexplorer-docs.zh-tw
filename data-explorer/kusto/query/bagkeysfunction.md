---
title: bag_keys() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 bag_keys()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a73798dff27bf9f4c7d554d97804aef6b49e1de2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518202"
---
# <a name="bag_keys"></a>bag_keys()

枚舉動態屬性包物件中的所有根鍵。

`bag_keys(`*動態物件*`)`

**傳回**

鍵陣列,順序尚未確定。

**範例**

|運算是|評估為|
|---|---|
|`bag_keys(dynamic({'a':'b', 'c':123}))` | `['a','c']`|
|`bag_keys(dynamic({'a':'b', 'c':{'d':123}})) `|`['a','c']`|
|`bag_keys(dynamic({'a':'b', 'c':[{'d':123}]})) `|`['a','c']`|
|`bag_keys(dynamic(null))`|`null`|
|`bag_keys(dynamic({}))`|`[]`|
|`bag_keys(dynamic('a'))`|`null`|
|`bag_keys(dynamic([]))  `|`null`|