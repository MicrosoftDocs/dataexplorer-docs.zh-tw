---
title: 月末() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的月末()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ebab067c730e3cd61c84ae33eba4e49d7f0b0c0c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515856"
---
# <a name="endofmonth"></a>endofmonth()

返回包含日期的月末,如果提供,則偏移量。

**語法**

`endofmonth(`*日期*`,`[*位移*]`)`

**引數**

* `date`:輸入日期。
* `offset`: 從輸入日期(整數,預設值 - 0)到可選的偏移月數。

**傳回**

表示給定*日期*值的月末的日期和時間,如果指定,則帶有偏移量。

**範例**

```kusto
  range offset from -1 to 1 step 1
 | project monthEnd = endofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|月末結束|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-31 23:59:59.9999999|
|2017-02-28 23:59:59.9999999|