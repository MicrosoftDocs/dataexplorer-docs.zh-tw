---
title: acos() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 acos()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: aa33714d57b319ba5a775385e8ee7d232addfe9d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519324"
---
# <a name="acos"></a>acos()

返回其成因為指定數位([`cos()`](cosfunction.md)的反向操作)的角度。

**語法**

`acos(`*X.*`)`

**引數**

* *x*: 範圍 [-1, 1] 中的實數。

**傳回**

* 圓弧幣值`x`
* `null`如果`x`<`x`-1 或 > 1