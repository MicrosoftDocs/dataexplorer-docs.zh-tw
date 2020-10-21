---
title: 'startofday ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 startofday ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 450863ec6360a40d1fea452dd0427e54f21c6c71
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241094"
---
# <a name="startofday"></a>startofday()

傳回包含日期的一天開始時間，如果有提供，則以位移位移。

## <a name="syntax"></a>語法

`startofday(`*日期*[ `,` *位移*]`)`

## <a name="arguments"></a>引數

* `date`：輸入日期。
* `offset`：輸入日期 (整數的選擇性位移天數，預設值為 0) 。 

## <a name="returns"></a>傳回

表示指定 *日期* 值之日開始的日期時間，如果有指定，則為位移。

## <a name="example"></a>範例

```kusto
  range offset from -1 to 1 step 1
 | project dayStart = startofday(datetime(2017-01-01 10:10:17), offset) 
```

|dayStart|
|---|
|2016-12-31 00：00：00.0000000|
|2017-01-01 00：00：00.0000000|
|2017-01-02 00：00：00.0000000|