---
title: 'avg ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 avg ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 949bd463f57b9eb0b674fe780aa50e78e30926a2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248045"
---
# <a name="avg-aggregation-function"></a>avg ( # A1 (彙總函式) 

計算整個群組中的 *Expr* 平均值。 

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

摘要 `avg(` *運算式*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 具有值的記錄 `null` 會被忽略，且不會包含在計算中。

## <a name="returns"></a>傳回

整個群組中 *Expr* 的平均值。
 