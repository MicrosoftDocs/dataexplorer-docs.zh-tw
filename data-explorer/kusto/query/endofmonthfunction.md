---
title: endofmonth （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 endofmonth （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 772cf42bfb4bd96a9cff94b7723b234139da462a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348287"
---
# <a name="endofmonth"></a>endofmonth()

傳回包含日期的月份結尾，如果有提供，則會位移位移。

## <a name="syntax"></a>語法

`endofmonth(`*日期*[ `,` *位移*]`)`

## <a name="arguments"></a>引數

* `date`：輸入日期。
* `offset`：輸入日期（整數，預設值為0）的選擇性位移月份數目。

## <a name="returns"></a>傳回

表示給定*日期*值月底的日期時間，如果有指定，則為位移。

## <a name="example"></a>範例

```kusto
  range offset from -1 to 1 step 1
 | project monthEnd = endofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|monthEnd|
|---|
|2016-12-31 23：59：59.9999999|
|2017-01-31 23：59：59.9999999|
|2017-02-28 23：59：59.9999999|