---
title: avg （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 avg （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: f058af075a856d12a2a6a81419f32b6efbd9ea16
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349392"
---
# <a name="avg-aggregation-function"></a>avg （）（彙總函式）

計算整個群組中*Expr*的平均值。 

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

總結 `avg(` *Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 具有值的記錄 `null` 會被忽略，而且不會包含在計算中。

## <a name="returns"></a>傳回

整個群組中*Expr*的平均值。
 