---
title: '弧度 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明在 Azure 資料總管中 ( # A1 的弧度。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bacb005b8828c63efac1737c527cc313a3ee052b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241330"
---
# <a name="radians"></a>radians()

使用公式將角度值以度數轉換成弧度值 `radians = (PI / 180 ) * angle_in_degrees`

## <a name="syntax"></a>語法

`radians(`*的*`)`

## <a name="arguments"></a>引數

* *答*： (實數) 的角度（以度為單位）。

## <a name="returns"></a>傳回

* 以度為單位的角度，以弧度為單位的對應角度。 

## <a name="examples"></a>範例

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|radians0|radians1|radians2|
|---|---|---|
|1.5707963267949|（3.14159265358979|6.28318530717959|