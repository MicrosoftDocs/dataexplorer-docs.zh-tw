---
title: endofweek （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 endofweek （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 57fa1764753e730f9ff0a2b01a70e0c221217d23
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348270"
---
# <a name="endofweek"></a>endofweek()

傳回包含日期的一周結束，如果有提供，則以位移移位。

一周的最後一天會被視為星期六。

## <a name="syntax"></a>語法

`endofweek(`*日期*[ `,` *位移*]`)`

## <a name="arguments"></a>引數

* `date`：輸入日期。
* `offset`：輸入日期的選擇性位移周數（整數，預設值-0）。

## <a name="returns"></a>傳回

表示給定*日期*值的一周結束的日期時間，如果有指定，則為位移。

## <a name="example"></a>範例

```kusto
  range offset from -1 to 1 step 1
 | project weekEnd = endofweek(datetime(2017-01-01 10:10:17), offset)  

```

|版|
|---|
|2016-12-31 23：59：59.9999999|
|2017-01-07 23：59：59.9999999|
|2017-01-14 23：59：59.9999999|