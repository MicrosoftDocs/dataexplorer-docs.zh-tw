---
title: 度數（）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的程度（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41d679ea1add3706de5012f4e4fbf382e1f7b3ee
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348372"
---
# <a name="degrees"></a>degrees()

使用公式將角度值以弧度轉換成值（以度為單位）`degrees = (180 / PI ) * angle_in_radians`

## <a name="syntax"></a>語法

`degrees(`*為*`)`

## <a name="arguments"></a>引數

* *a*：以弧度表示的角度（實數）。

## <a name="returns"></a>傳回

* 以弧度指定之角度的對應角度（以度為單位）。 

## <a name="examples"></a>範例

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|degrees0|degrees1|degrees2|
|---|---|---|
|45|270|0|
