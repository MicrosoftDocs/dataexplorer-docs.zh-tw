---
title: facet 運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 facet 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0e5bc062b99a97b8d11c11312aac2d5829d6584b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348015"
---
# <a name="facet-operator"></a>facet 運算子

傳回一組資料表，每個指定的資料行一個。
每個資料表都會指定其資料行所採用的值清單。
您可以使用子句來建立其他資料表 `with` 。

## <a name="syntax"></a>語法

*T* `| facet by` *ColumnName* [ `, ` ...] [ `with (` *filterPipe*`)`

## <a name="arguments"></a>引數

* *ColumnName：* 輸入中要摘要為輸出資料表的資料行名稱。
* *filterPipe：* 套用至輸入資料表的查詢運算式，用來產生其中一個輸出。

## <a name="returns"></a>傳回

多個資料表：一個用於 `with` 子句，一個用於每個資料行。

## <a name="example"></a>範例

```kusto
MyTable 
| facet by city, eventType 
    with (where timestamp > ago(7d) | take 1000)
```