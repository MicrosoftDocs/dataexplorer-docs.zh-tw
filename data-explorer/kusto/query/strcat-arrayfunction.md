---
title: strcat_array （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 strcat_array （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b8369d2e994477c9d01880632fac5f8a3ebaf6a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342592"
---
# <a name="strcat_array"></a>strcat_array()

使用指定的分隔符號，建立陣列值的串連字號串。
    
## <a name="syntax"></a>語法

`strcat_array(`*陣列*、*分隔符號*`)`

## <a name="arguments"></a>引數

* *array*： `dynamic` 代表要串連之值陣列的值。
* *分隔符號*： `string` 將用來串連*array*中的值的值

## <a name="returns"></a>傳回

陣列值，串連成單一字串。

## <a name="examples"></a>範例
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|字串|
|---|
|1->2->3|