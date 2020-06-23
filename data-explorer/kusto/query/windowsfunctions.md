---
title: 視窗函式-Azure 資料總管
description: 本文說明 Azure 資料總管中的視窗功能。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 1e93764058c6d890568e90f67472ec4306a092f8
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128694"
---
# <a name="window-functions-overview"></a>Window 函式概觀

視窗函式會在一次資料列集中的多個資料列（記錄）上運作。 不同于彙總函式，視窗函式需要序列化資料列集中的資料列（具有特定的順序）。 視窗函數可能相依于判斷結果的順序。

視窗函數只能用於序列化的集合。 序列化資料列集最簡單的方式是使用[序列化運算子](./serializeoperator.md)。 這個運算子會以任意方式「凍結」資料列的順序。 如果序列化資料列的順序在語義上很重要，請使用[sort 運算子](./sortoperator.md)來強制執行特定的順序。

序列化程式與它有相關聯的非一般成本。 例如，它可能會在許多情況下阻止查詢平行處理原則。 因此，請勿在不必要的情況下套用序列化。 如有必要，請重新排列查詢，以便在可能的最小資料列集上執行序列化。

## <a name="serialized-row-set"></a>序列化資料列集

您可以使用下列其中一種方式來序列化任意資料列集（例如資料表或表格式運算子的輸出）：

1. 藉由排序資料列集。 如需發出排序資料列集的運算子清單，請參閱下方。
2. 使用[序列化運算子](./serializeoperator.md)。

許多表格式運算子都會在輸入已經序列化時序列化輸出，即使運算子本身並不保證會序列化結果。 例如，這個屬性保證會用於[擴充運算子](./extendoperator.md)、[專案運算子](./projectoperator.md)和[where 運算子](./whereoperator.md)。

## <a name="operators-that-emit-serialized-row-sets-by-sorting"></a>藉由排序發出序列化資料列集的運算子

* [order 運算子](./orderoperator.md)
* [sort 運算子](./sortoperator.md)
* [top 運算子](./topoperator.md)
* [top-hitters 運算子](./tophittersoperator.md)
* [top-nested 運算子](./topnestedoperator.md)

## <a name="operators-that-preserve-the-serialized-row-set-property"></a>保留序列化資料列集屬性的運算子

* [extend 運算子](./extendoperator.md)
* [mv-expand 運算子](./mvexpandoperator.md)
* [parse 運算子](./parseoperator.md)
* [project 運算子](./projectoperator.md)
* [project-away 運算子](./projectawayoperator.md)
* [project-rename 運算子](./projectrenameoperator.md)
* [take 運算子](./takeoperator.md)
* [where 運算子](./whereoperator.md)
