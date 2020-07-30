---
title: treepath （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 treepath （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e52caa6da7d5746119e363d1676b39fcd9da0340
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350650"
---
# <a name="treepath"></a>treepath()

列舉所有可識別動態物件中分葉的路徑運算式。

`treepath(`*動態物件*`)`

## <a name="returns"></a>傳回

路徑運算式的陣列。

## <a name="examples"></a>範例

|運算是|評估為|
|---|---|
|`treepath(parse_json('{"a":"b", "c":123}'))` | `["['a']","['c']"]`|
|`treepath(parse_json('{"prop1":[1,2,3,4], "prop2":"value2"}'))`|`["['prop1']","['prop1'][0]","['prop2']"]`|
|`treepath(parse_json('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))`|`["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]`|