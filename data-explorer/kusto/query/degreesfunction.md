---
title: 度 () - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的度()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1365d6c6629f4f4769d7c4b62491eaec25e4ec59
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516026"
---
# <a name="degrees"></a>degrees()

使用公式將弧度的角度值轉換為以度表示的值`degrees = (180 / PI ) * angle_in_radians`

**語法**

`degrees(`*a*`)`

**引數**

* *a*: 以弧度角(實數)

**傳回**

* 以弧度為單位指定角度的相應角度。 

**範例**

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|度0|度1|度2|
|---|---|---|
|45|270|0|
