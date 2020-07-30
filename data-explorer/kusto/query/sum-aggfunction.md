---
title: sum （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 sum （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3053a2c3bd423a2e1b8a2690bcdf54de9f1a1e36
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350820"
---
# <a name="sum-aggregation-function"></a>sum （）（彙總函式）

計算整個群組的*Expr*總和。 

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

總結 `sum(` *Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。 

## <a name="returns"></a>傳回

整個群組中*Expr*的總和值。
 