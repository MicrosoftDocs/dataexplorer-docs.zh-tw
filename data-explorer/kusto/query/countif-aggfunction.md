---
title: 計數(聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 countif()聚合函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 03b6f1cb959706463a73d8aa18a11144e2123492
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516978"
---
# <a name="countif-aggregation-function"></a>計數() (聚合函數)

傳回 Predicate** 評估為 `true` 的資料列計數。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

另請參閱 - [count()](count-aggfunction.md)函數,該函數對沒有謂詞表達式的行進行計數。

**語法**

匯總`countif(`*謂詞*`)`

**引數**

* *謂詞*:將用於聚合計算的運算式。 

**傳回**

傳回 Predicate** 評估為 `true` 的資料列計數。

> [!TIP]
> 使用 `summarize countif(filter)` 來取代 `where filter | summarize count()`