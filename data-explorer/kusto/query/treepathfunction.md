---
title: 樹路徑() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的樹路徑。"
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 27a0edc61a1d2317427454aaf74531ec395d067d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505690"
---
# <a name="treepath"></a>treepath()

列舉所有可識別動態物件中分葉的路徑運算式。

`treepath(`*動態物件*`)`

**傳回**

路徑運算式的陣列。

**範例**

|運算是|評估為|
|---|---|
|`treepath(parse_json('{"a":"b", "c":123}'))` | `["['a']","['c']"]`|
|`treepath(parse_json('{"prop1":[1,2,3,4], "prop2":"value2"}'))`|`["['prop1']","['prop1'][0]","['prop2']"]`|
|`treepath(parse_json('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))`|`["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]`|