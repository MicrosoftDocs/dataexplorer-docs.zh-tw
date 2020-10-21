---
title: 'endofyear ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 endofyear ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a93b24af949beedc02ddd3dbbc479074f0aa891c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249143"
---
# <a name="endofyear"></a>endofyear()

傳回包含日期的年份結尾，如有提供，則會位移位移。

## <a name="syntax"></a>語法

`endofyear(`*日期*[ `,` *位移*]`)`

## <a name="arguments"></a>引數

* `date`：輸入日期。
* `offset`：輸入日期 (整數的選擇性位移年數，預設值為 0) 。

## <a name="returns"></a>傳回

表示指定 *日期* 值之年份結尾的日期時間，如果有指定，則為位移。

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