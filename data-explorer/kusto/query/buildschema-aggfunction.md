---
title: 'buildschema ( # A1 (彙總函式) -Azure 資料總管'
description: '本文說明 Azure 資料總管中的 buildschema ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3fb6ae643bb4350cea1ffef4493625bc9c7d191d
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793577"
---
# <a name="buildschema-aggregation-function"></a>buildschema ( # A1 (彙總函式) 

傳回認可所有 *DynamicExpr*值的最基本架構。

* 只能用在匯總的內容中，在[摘要](summarizeoperator.md)中

## <a name="syntax"></a>語法

摘要 `buildschema(` *DynamicExpr*`)`

## <a name="arguments"></a>引數

* *DynamicExpr*：用於匯總計算的運算式。 參數資料行類型必須為 `dynamic` 。 

## <a name="returns"></a>傳回

群組之間的最大值 *`Expr`* 。

> [!TIP] 
> 如果 `buildschema(json_column)` 提供語法錯誤：
>
> > *您是 `json_column` 字串，而不是動態物件嗎？*
>
> 然後使用 `buildschema(parsejson(json_column))` 。

## <a name="example"></a>範例

假設輸入資料行有三個動態值。

||
|---|
|`{"x":1, "y":3.5}`|
|`{"x":"somevalue", "z":[1, 2, 3]}`|
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`|
||

所產生的結構描述將是︰

```kusto
{ 
    "x":["int", "string"],
    "y":["double", {"w": "string"}],
    "z":{"`indexer`": ["int", "string"]},
    "t":{"`indexer`": "string"}
}
```

結構描述指出：

* 根物件是具有四個屬性的容器，名為 x、y、z 和 t。
* 名為 "x" 的屬性，其類型可能是 "int" 或 "string" 類型。
* 名為 "y" 的屬性，其類型可能為 "double"，或另一個具有類型 "string" 的屬性稱為 "w" 的容器。
* ``indexer`` 關鍵字指出 "z" 與 "t" 是陣列。
* 陣列 "z" 中的每個專案都屬於類型 "int" 或 "string" 類型。
* "t" 是字串陣列。
* 每個屬性皆可隱含選用，且任何陣列都可以是空的。

### <a name="schema-model"></a>結構描述模型

所傳回結構描述的語法如下︰

```output
Container ::= '{' Named-type* '}';
Named-type ::= (name | '"`indexer`"') ':' Type;
Type ::= Primitive-type | Union-type | Container;
Union-type ::= '[' Type* ']';
Primitive-type ::= "int" | "string" | ...;
```

值相當於 TypeScript 類型注釋的子集，編碼為 Kusto 動態值。 在 Typescript 中，範例結構描述會是︰

```typescript
var someobject: 
{
    x?: (number | string),
    y?: (number | { w?: string}),
    z?: { [n:number] : (int | string)},
    t?: { [n:number]: string }
}
```
