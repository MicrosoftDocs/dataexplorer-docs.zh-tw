---
title: strcat_array)- Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的strcat_array()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d2412762cf68243e3952a8ad12a5b919d947bd3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506948"
---
# <a name="strcat_array"></a>strcat_array()

使用指定的分隔符建立串聯的陣元值字串。
    
**語法**

`strcat_array(`*陣列*,*分隔符*`)`

**引數**

* *陣列*`dynamic`: 表示要串聯的值陣列的值。
* *分值器*`string`: 將用於串聯*陣列*中的值值值

**傳回**

陣列值,串聯到單個字串。

**範例**
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|字串|
|---|
|1->2->3|