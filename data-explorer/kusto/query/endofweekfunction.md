---
title: 'endofweek ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 endofweek ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 47a5c9dd11af7282c38aabdd08b8b3bec571e57a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253027"
---
# <a name="endofweek"></a>endofweek()

傳回包含日期的周結束，如有提供，則會位移位移。

一周的最後一天會視為星期六。

## <a name="syntax"></a>語法

`endofweek(`*日期*[ `,` *位移*]`)`

## <a name="arguments"></a>引數

* `date`：輸入日期。
* `offset`：輸入日期 (整數的選擇性位移周數，預設值為 0) 。

## <a name="returns"></a>傳回

表示指定 *日期* 值之周結束的日期時間，如果有指定，則為位移。

## <a name="example"></a>範例

```kusto
  range offset from -1 to 1 step 1
 | project weekEnd = endofweek(datetime(2017-01-01 10:10:17), offset)  

```

|週末|
|---|
|2016-12-31 23：59：59.9999999|
|2017-01-07 23：59：59.9999999|
|2017-01-14 23：59：59.9999999|