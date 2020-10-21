---
title: 'column_ifexists ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 column_ifexists。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 570a832673d94d57d69dfa9c8442d56802ecd7ac
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247875"
---
# <a name="column_ifexists"></a>column_ifexists()

使用資料行名稱做為字串和預設值。 傳回資料行的參考（如果有的話），否則傳回預設值。

## <a name="syntax"></a>語法

`column_ifexists(`*columnName* `, `*defaultValue*) 

## <a name="arguments"></a>引數

* *columnName*：資料行的名稱
* *defaultValue*：當資料行不存在於用來使用函數的內容中時，所要使用的值。
                  這個值可以是任何純量運算式 (例如) 的另一個資料行的參考。

## <a name="returns"></a>傳回

如果 *columnName* 存在，則為它所參考的資料行。 否則為- *defaultValue*。

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