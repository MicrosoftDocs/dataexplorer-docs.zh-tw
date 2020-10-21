---
title: 'startofmonth ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 startofmonth ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c7e13cf809174fd258f9e7245bb2e537e2408c28
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241746"
---
# <a name="startofmonth"></a>startofmonth()

傳回包含日期的月份開頭，如有提供，則會位移位移。

## <a name="syntax"></a>語法

`startofmonth(`*日期*[ `,` *位移*]`)`

## <a name="arguments"></a>引數

* `date`：輸入日期。
* `offset`：輸入日期 (整數的選擇性位移月份數，預設值為 0) 。

## <a name="returns"></a>傳回

表示指定 *日期* 值之月份開頭的日期時間，如果有指定，則為位移。

## <a name="example"></a>範例

```kusto
  range offset from -1 to 1 step 1
 | project monthStart = startofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|monthStart|
|---|
|2016-12-01 00：00：00.0000000|
|2017-01-01 00：00：00.0000000|
|2017-02-01 00：00：00.0000000|