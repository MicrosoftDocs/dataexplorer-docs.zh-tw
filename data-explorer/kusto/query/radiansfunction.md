---
title: 弧度（）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的弧度（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3aaa41a631498e2938acf722b75f409a1bbe5031
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345941"
---
# <a name="radians"></a>radians()

使用公式將角度值以弧度轉換成值`radians = (PI / 180 ) * angle_in_degrees`

## <a name="syntax"></a>語法

`radians(`*為*`)`

## <a name="arguments"></a>引數

* *a*：角度（以度為單位）（實數）。

## <a name="returns"></a>傳回

* 以度為單位所指定角度的對應角度（弧度）。 

## <a name="examples"></a>範例

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|radians0|radians1|radians2|
|---|---|---|
|1.5707963267949|3.14159265358979|6.28318530717959|