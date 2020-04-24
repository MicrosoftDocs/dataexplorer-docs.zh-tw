---
title: sum() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 sum()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b729d9be8aba9683a053ef80acb3936c0c64d6a8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506676"
---
# <a name="sum-aggregation-function"></a>總() (聚合函數)

計算整個組的*Expr*的總和。 

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

總結`sum(` *Expr*`)`

**引數**

* *Expr*:將用於聚合計算的運算式。 

**傳回**

跨組中*Expr*的總和值。
 