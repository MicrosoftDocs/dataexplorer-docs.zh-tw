---
title: 弧度() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 radians()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0dc04c5f9593b6bd5fc61f57d20819cf7d2a178c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510654"
---
# <a name="radians"></a>radians()

使用公式將角度值 (以度表示弧度)轉換為值`radians = (PI / 180 ) * angle_in_degrees`

**語法**

`radians(`*a*`)`

**引數**

* *a*: 角度(以度表示)。

**傳回**

* 以度為單位指定角度的相應角度( 

**範例**

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|弧度0|弧度1|弧度2|
|---|---|---|
|1.5707963267949|3.14159265358979|6.28318530717959|