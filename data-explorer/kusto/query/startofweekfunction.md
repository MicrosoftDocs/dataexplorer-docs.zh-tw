---
title: 周開始() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的周初()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9705b586a0b8c5161b7d4c27735f644b6982c707
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507220"
---
# <a name="startofweek"></a>startofweek()

返回包含日期的周開始,如果提供,則偏移偏移。

一周的開始被認為是一個星期天。

**語法**

`startofweek(`*日期*`,`[*位移*]`)`

**引數**

* `date`:輸入日期。
* `offset`: 從輸入日期(整數,預設值 - 0)到可選的偏移周數。

**傳回**

表示給定*日期*值的周開始日期時間,如果指定,則帶有偏移量。

**範例**

```kusto
  range offset from -1 to 1 step 1
 | project weekStart = startofweek(datetime(2017-01-01 10:10:17), offset) 
```

|周開始|
|---|
|2016-12-25 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-08 00:00:00.0000000|