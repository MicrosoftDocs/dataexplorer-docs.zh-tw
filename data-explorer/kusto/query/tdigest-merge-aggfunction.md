---
title: tdigest_merge() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的tdigest_merge()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 0b7de916dd53c19a49301c8048e2d8867d1b1249
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506387"
---
# <a name="tdigest_merge-aggregation-function"></a>tdigest_merge () (聚合函數)

合併整個組中的 t 消化結果。 

* 只能在[匯總](summarizeoperator.md)中的聚合上下文中使用。

此專案可以閱讀更多有關基礎演演算法 (T-Digest) 和估計誤差[。](percentiles-aggfunction.md#estimation-error-in-percentiles)

**語法**

總結`tdigest_merge(` *Expr*`)`.

總結`tdigest_merge(` *Expr* `)` - 別名。

**引數**

* *Expr*:將用於聚合計算的運算式。 

**傳回**

跨組中*Expr*的合併 tdigest 值。
 

**技巧**

1) 您可以使用函數 [`percentile_tdigest()`] (percentile-tdigestfunction.md)。

2) 同一組中包含的所有 tdigest 都必須是同一類型。