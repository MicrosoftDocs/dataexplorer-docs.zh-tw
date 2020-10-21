---
title: '度數 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 的度數。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 04efbee59bce153de76ab5d44617b8d1bebc121c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253085"
---
# <a name="degrees"></a>degrees()

使用公式將弧度的角度值以度數轉換成值（以度為單位） `degrees = (180 / PI ) * angle_in_radians`

## <a name="syntax"></a>語法

`degrees(`*的*`)`

## <a name="arguments"></a>引數

* *答*：以弧度為單位的角度 (實數) 。

## <a name="returns"></a>傳回

* 以弧度為單位的相對應角度（以弧度為單位）。 

## <a name="examples"></a>範例

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|degrees0|degrees1|degrees2|
|---|---|---|
|45|270|0|
