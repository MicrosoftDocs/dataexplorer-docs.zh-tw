---
title: startofweek （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 startofweek （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 24763297585a7f043847e3037103a61650f65bd1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87343476"
---
# <a name="startofweek"></a>startofweek()

傳回包含日期的一周開始時間，如果有提供，則會位移位移。

一周開始會視為星期日。

## <a name="syntax"></a>語法

`startofweek(`*日期*[ `,` *位移*]`)`

## <a name="arguments"></a>引數

* `date`：輸入日期。
* `offset`：輸入日期的選擇性位移周數（整數，預設值-0）。

## <a name="returns"></a>傳回

表示給定*日期*值一周開始的日期時間，如果有指定，則為位移。

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