---
title: avg() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 vg()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: aadc756bdf4c6cab805f58a8a600815cf29680f7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518338"
---
# <a name="avg-aggregation-function"></a>平均() (聚合函數)

計算整個組中*Expr*的平均值。 

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

總結`avg(` *Expr*`)`

**引數**

* *Expr*:將用於聚合計算的運算式。 值`null`的記錄將被忽略,並且不包括在計算中。

**傳回**

整個組中*Expr*的平均值。
 