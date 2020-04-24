---
title: 舍() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 round()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 05111b6261c14f21d8d08e88a4c070faab32dc4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510229"
---
# <a name="round"></a>round()

將舍入源返回到指定的精度。

**語法**

`round(`*來源*`,`[*精度*]`)`

**引數**

* *來源*: 在上計算圓的來源標量。
* *精度*:源將舍入到的數字數。( 預設值為 0)

**傳回**

到指定精度的舍入源。

捨入不同於[`bin()`](binfunction.md)/[`floor()`](floorfunction.md)第一輪數位到特定數字數,而最後一輪值為給定條箱大小的整數倍數(圓形(2.15,1)返回 2.2,而 bin(2.15, 1) 傳回 2)。
 

**範例**

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```