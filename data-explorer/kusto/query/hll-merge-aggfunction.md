---
title: hll_merge() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的hll_merge(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 4700d5c87bf0f29f7bba86d56114a6a61092da94
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514105"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge() (聚合函數)

將整個組的 HLL 結果合併為單個 HLL 值。

* 只能在[匯總](summarizeoperator.md)中的聚合上下文中使用。

閱讀有關[基礎演演算法 *(H*yper*L*og*L*og) 和估計精度](dcount-aggfunction.md#estimation-accuracy)。

**語法**

`summarize``hll_merge(` *Expr*`)`

**引數**

* *Expr*:將用於聚合計算的運算式。 

**傳回**

跨組中*Expr*的合併 hll 值。
 
**技巧**

1) 您可以使用函數 [dcount_hll] (dcount-hllfunction.md),該函數將從 hll / hll-merge 聚合函數中計算 dcount。