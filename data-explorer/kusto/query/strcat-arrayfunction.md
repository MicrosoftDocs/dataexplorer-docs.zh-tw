---
title: 'strcat_array ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 strcat_array。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 06bdf7eb55b61cb974e803c9cdeb7b37b34ad87e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243789"
---
# <a name="strcat_array"></a>strcat_array()

使用指定的分隔符號，建立陣列值的串連字號串。
    
## <a name="syntax"></a>語法

`strcat_array(`*陣列*、 *分隔符號*`)`

## <a name="arguments"></a>引數

* *陣列*： `dynamic` 代表要串連之值陣列的值。
* *分隔符號*： `string` 將用來串連*陣列*值的值

## <a name="returns"></a>傳回

陣列值，串連成單一字串。

## <a name="examples"></a>範例
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|字串|
|---|
|1->2->3|