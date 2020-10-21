---
title: 'startofweek ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 startofweek ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5f53dfca4183c4dae623b41abf4c5bce6a3e828
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241182"
---
# <a name="startofweek"></a>startofweek()

傳回包含日期的周次開始，如有提供，則會位移位移。

一周開始會被視為星期日。

## <a name="syntax"></a>語法

`startofweek(`*日期*[ `,` *位移*]`)`

## <a name="arguments"></a>引數

* `date`：輸入日期。
* `offset`：輸入日期 (整數的選擇性位移周數，預設值為 0) 。

## <a name="returns"></a>傳回

表示指定 *日期* 值之周開始的日期時間，如果有指定，則為位移。

## <a name="example"></a>範例

```kusto
  range offset from -1 to 1 step 1
 | project weekStart = startofweek(datetime(2017-01-01 10:10:17), offset) 
```

|weekStart|
|---|
|2016-12-25 00：00：00.0000000|
|2017-01-01 00：00：00.0000000|
|2017-01-08 00：00：00.0000000|