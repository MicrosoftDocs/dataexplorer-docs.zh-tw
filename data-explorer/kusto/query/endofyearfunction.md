---
title: endofyear （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 endofyear （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a76a402725eaefe9f12cbb67228381e3b0c25351
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348253"
---
# <a name="endofyear"></a>endofyear()

傳回包含日期的年份結尾，如果有提供，則會位移位移。

## <a name="syntax"></a>語法

`endofyear(`*日期*[ `,` *位移*]`)`

## <a name="arguments"></a>引數

* `date`：輸入日期。
* `offset`：輸入日期（整數，預設值為0）的選擇性位移年份數目。

## <a name="returns"></a>傳回

表示給定*日期*值年份結束的日期時間，如果有指定，則為位移。

## <a name="example"></a>範例

```kusto
  range offset from -1 to 1 step 1
 | project yearEnd = endofyear(datetime(2017-01-01 10:10:17), offset) 
```

|yearEnd|
|---|
|2016-12-31 23：59：59.9999999|
|2017-12-31 23：59：59.9999999|
|2018-12-31 23：59：59.9999999|