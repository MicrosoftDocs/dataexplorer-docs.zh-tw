---
title: 年初() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的年初一。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f91c749cc3833954d902eb4ebd7e230e32e3a991
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507203"
---
# <a name="startofyear"></a>startofyear()

返回包含日期的年初,如果提供,則偏移。

**語法**

`startofyear(`*日期*`,`[*位移*]`)`

**引數**

* `date`:輸入日期。
* `offset`: 從輸入日期(整數,預設值 - 0)到可選的偏移年數。 

**傳回**

表示給定*日期*值的年初的約會時間,如果指定,則帶有偏移量。

**範例**

```kusto
  range offset from -1 to 1 step 1
 | project yearStart = startofyear(datetime(2017-01-01 10:10:17), offset) 
```

|年份開始|
|---|
|2016-01-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2018-01-01 00:00:00.0000000|