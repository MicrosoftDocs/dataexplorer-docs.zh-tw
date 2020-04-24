---
title: 最小() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 min()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/24/2019
ms.openlocfilehash: ca50210c84b39f19e6717b27089313d0d116e21a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512405"
---
# <a name="min-aggregation-function"></a>最小 () (聚合函數)

返回整個組的最小值。 

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

`summarize``min(` *Expr*`)`

**引數**

* *Expr*:將用於聚合計算的運算式。 

**傳回**

跨組的*Expr*的最小值。
 
> [!TIP]
> 這為您提供了最小或最大值本身 - 例如,最高或最低價格。 但是,如果您希望行中的其他列(例如,價格最低的供應商的名稱)使用[arg_max](arg-max-aggfunction.md)或[arg_min。](arg-min-aggfunction.md)