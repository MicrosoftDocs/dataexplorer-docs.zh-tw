---
title: facet 運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 facet 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2398652e7cad7456d294f11353cfdfe049080503
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249052"
---
# <a name="facet-operator"></a>facet 運算子

傳回一組資料表，每個指定的資料行各一個。
每個資料表都會指定其資料行所採用的值清單。
您可以使用子句來建立額外的資料表 `with` 。

## <a name="syntax"></a>語法

*T* `| facet by` *ColumnName* [ `, ` ...] [ `with (` *filterPipe*`)`

## <a name="arguments"></a>引數

* *ColumnName：* 輸入中資料行的名稱，要摘要為輸出資料表。
* *filterPipe：* 套用至輸入資料表以產生其中一個輸出的查詢運算式。

## <a name="returns"></a>傳回

多個資料表：一個用於 `with` 子句，另一個用於每個資料行。

## <a name="example"></a>範例

```kusto
MyTable 
| facet by city, eventType 
    with (where timestamp > ago(7d) | take 1000)
```