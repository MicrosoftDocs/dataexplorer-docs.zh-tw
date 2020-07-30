---
title: column_ifexists （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 column_ifexists （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5eeecf9e4756ac18cdeb5c6297aea1bcca5bac14
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348848"
---
# <a name="column_ifexists"></a>column_ifexists()

接受資料行名稱做為字串和預設值。 傳回資料行的參考（如果有的話），否則傳回預設值。

## <a name="syntax"></a>語法

`column_ifexists(`*columnName* `, `*defaultValue*）

## <a name="arguments"></a>引數

* *columnName*：資料行的名稱
* *defaultValue*：當資料行不存在於用於函數的內容中時，所要使用的值。
                  這個值可以是任何純量運算式（例如另一個資料行的參考）。

## <a name="returns"></a>傳回

如果*columnName*存在，則為它所參考的資料行。 否則為- *defaultValue*。

## <a name="examples"></a>範例

```kusto
.create function with (docstring = "Wraps a table query that allows querying the table even if columnName doesn't exist ", folder="My Functions")
ColumnOrDefault(tableName:string, columnName:string)
{
    // There's no column "Capital" in "StormEvents", therefore, the State column will be used instead
    table(tableName) | project column_ifexists(columnName, State)
}


ColumnOrDefault("StormEvents", "Captial");
```