---
title: 'array_concat ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 array_concat。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4c8e2da4d2ba4ed205987b5a1d063ac2e75ed289
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246909"
---
# <a name="array_concat"></a>array_concat()

將許多動態陣列串連到單一陣列。

## <a name="syntax"></a>語法

`array_concat(`*arr1* `[` ， ` *arr2*, ...]`) '

## <a name="arguments"></a>引數

* *arr1 .。。arrN*：要串連成動態陣列的輸入陣列。 所有引數都必須是動態陣列 (請參閱 [pack_array](packarrayfunction.md)) 。 

## <a name="returns"></a>傳回

具有 arr1、arr2、...、arrN 之陣列的動態陣列。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|資料行1|
|---|
|[1，2，4，1，2]|
|[2，4，8，2，4]|
|[3，6，12，3，6]|
