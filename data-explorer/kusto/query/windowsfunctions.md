---
title: 視窗函數 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的視窗函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: ab4f6da2478ba4de81b2034c1cb07458daa80bd0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504245"
---
# <a name="window-functions"></a>視窗函式

視窗函數一次對一個行集中的多行(記錄)進行操作。
與聚合函數不同,它們要求對行集中的行**進行序列化**(對行具有特定順序),因為視窗函數可能取決於確定結果的順序。

視窗函數不能用於未序列化的列集,並且在查詢在此類上下文中使用時將出現錯誤。 序列化列集的最簡單方法是使用[序列化運算符](./serializeoperator.md),它只需「凍結」行的順序(以某些未指定的任意方式)。
如果序列化行的順序在語義上很重要,則可以使用[排序運算符](./sortoperator.md)強制特定順序。

序列化過程具有與其關聯的非平凡成本。 例如,它可能防止查詢並行性在許多情況下。 因此,強烈建議不要不必要地應用序列化,並在必要時重新排列查詢,以便在盡可能少的行集中執行序列化。

## <a name="serialized-row-set"></a>序列化列集

可以以下方式之一對任意列集(如表或表格運算符的輸出)進行序列化:

1. 通過對行集進行排序。 有關發出已排序行集的運算符的清單,請參閱下文。
2. 使用[序列化運算子](./serializeoperator.md)。

請注意,許多表格運算元雖然本身並不能保證其結果是序列化的,但它們具有這樣的屬性:如果輸入是序列化的,則輸出也是如此。 例如,此屬性為[擴展運算子](./extendoperator.md)、[專案運算子](./projectoperator.md)和[where 運算符](./whereoperator.md)保證。

## <a name="operators-that-emit-serialized-row-sets-by-sorting"></a>以排序發出序列化列集的運算子

* [order 運算子](./orderoperator.md)
* [sort 運算子](./sortoperator.md)
* [頂端運算子](./topoperator.md)
* [top-hitters 運算子](./tophittersoperator.md)
* [top-nested 運算子](./topnestedoperator.md)

## <a name="operators-that-preserve-the-serialized-row-set-property"></a>保留序列化列集屬性的運算子

* [extend 運算子](./extendoperator.md)
* [mv-展開運算子](./mvexpandoperator.md)
* [parse 運算子](./parseoperator.md)
* [project 運算子](./projectoperator.md)
* [project-away 運算子](./projectawayoperator.md)
* [project-rename 運算子](./projectrenameoperator.md)
* [take 運算子](./takeoperator.md)
* [其中操作員](./whereoperator.md)