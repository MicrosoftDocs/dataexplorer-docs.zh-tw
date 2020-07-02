---
title: buildschema （）（彙總函式）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 buildschema （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: cf21443beffb327e2708b8990017ac37fbbc8d21
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85902045"
---
# <a name="buildschema-aggregation-function"></a>buildschema （）（彙總函式）

傳回 dynamicexpression 所有*DynamicExpr*值的最小架構。

* 只能在匯總的內容中使用，在[摘要](summarizeoperator.md)中

**語法**

摘要 `buildschema(` *DynamicExpr*`)`

**引數**

* *DynamicExpr*：用於匯總計算的運算式。 參數資料行類型必須是 `dynamic` 。 

**傳回**

整個群組的最大值 *`Expr`* 。

> [!TIP] 
> 如果出現 `buildschema(json_column)` 語法錯誤：*是您 `json_column` 的字串，而不是動態物件嗎？* 然後使用 `buildschema(parsejson(json_column))` 。

**範例**

假設輸入資料行有三個動態值。

||
|---|
|`{"x":1, "y":3.5}`|
|`{"x":"somevalue", "z":[1, 2, 3]}`|
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`|
||

所產生的結構描述將是︰

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

結構描述指出：

* 根物件是具有四個屬性（名為 x、y、z 和 t）的容器。
* 名為 "x" 的屬性，其類型可能是 "int" 或 "string" 類型。
* 名為 "y" 的屬性可能是 "double" 類型，或另一個具有 "w" 類型 "string" 屬性的容器。
* ``indexer`` 關鍵字指出 "z" 與 "t" 是陣列。
* 陣列 "z" 中的每個專案都屬於類型 "int" 或類型 "string"。
* "t" 是字串陣列。
* 每個屬性皆可隱含選用，且任何陣列都可以是空的。

### <a name="schema-model"></a>結構描述模型

所傳回結構描述的語法如下︰

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

這些值相當於 TypeScript 類型注釋的子集，並編碼為 Kusto 動態值。 在 Typescript 中，範例結構描述會是︰

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }
    
