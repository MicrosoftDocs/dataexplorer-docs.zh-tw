---
title: set_has_element （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 set_has_element （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 314244c58eca6082b9042b263e6b3e6faeb69840
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351194"
---
# <a name="set_has_element"></a>set_has_element()

判斷指定的集合是否包含指定的元素。

## <a name="syntax"></a>語法

`set_has_element(`*陣列*，*值*`)`

## <a name="arguments"></a>引數

* *array*：要搜尋的輸入陣列。
* *value*：要搜尋的值。 值應為、、、、、、 `long` `integer` 或類型 `double` `datetime` `timespan` `decimal` `string` `guid` 。

## <a name="returns"></a>傳回

[True] 或 [false]，視值是否存在於陣列中而定。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|結果|
|---|
|1|

**另請參閱**

使用 [`array_index_of(arr, value)`](arrayindexoffunction.md) 來尋找陣列中的值所在位置。 這兩個函數的效能都相同。
