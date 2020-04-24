---
title: asin() - Azure 資料資源管理員 |微軟文件
description: 本文在 Azure 資料資源管理器中介紹 asin()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 5db112daeba59dd841b02df8ba1a41185654ad6a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518542"
---
# <a name="asin"></a>asin()

返回正著子為指定數位([`sin()`](sinfunction.md)的反向操作)的角度。

**語法**

`asin(`*X.*`)`

**引數**

* *x*: 範圍 [-1, 1] 中的實數。

**傳回**

* 弧正著的值`x`
* `null`如果`x`<`x`-1 或 > 1