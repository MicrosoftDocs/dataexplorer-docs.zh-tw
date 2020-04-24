---
title: 結束() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的尾天()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a25f6b9c4ba660fbb8a50390d9d6920ff21caeb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515873"
---
# <a name="endofday"></a>endofday()

返回包含日期的一天結束,如果提供,則偏移偏移。

**語法**

`endofday(`*日期*`,`[*位移*]`)`

**引數**

* `date`:輸入日期。
* `offset`: 從輸入日期(整數,預設值 - 0)到偏移天數的可選天數。

**傳回**

表示給定*日期*值的一天結束的日期時間,如果指定,則帶有偏移量。

**範例**

```kusto
  range offset from -1 to 1 step 1
 | project dayEnd = endofday(datetime(2017-01-01 10:10:17), offset) 
```

|日結束|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-01 23:59:59.9999999|
|2017-01-02 23:59:59.9999999|