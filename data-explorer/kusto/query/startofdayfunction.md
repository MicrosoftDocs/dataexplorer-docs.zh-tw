---
title: 開始() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的開始數據()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6517b50ca880085761212ae9037cee96d20a3269
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507271"
---
# <a name="startofday"></a>startofday()

返回包含日期的開始日期,如果提供,則偏移偏移。

**語法**

`startofday(`*日期*`,`[*位移*]`)`

**引數**

* `date`:輸入日期。
* `offset`: 從輸入日期(整數,預設值 - 0)到偏移天數的可選天數。 

**傳回**

表示給定*日期*值的一天開始的日期時間,如果指定,則帶有偏移量。

**範例**

```kusto
  range offset from -1 to 1 step 1
 | project dayStart = startofday(datetime(2017-01-01 10:10:17), offset) 
```

|日開始|
|---|
|2016-12-31 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-02 00:00:00.0000000|