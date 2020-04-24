---
title: 產生架構()(聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的生成架構(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d0faceae970034ae96ced2b8958af4f16cb5dff4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517284"
---
# <a name="buildschema-aggregation-function"></a>產生架構 () (聚合函數)

返回允許*DynamicExpr*的所有值的最小架構。

* 只能在[匯總](summarizeoperator.md)的聚合上下文中使用

**語法**

總結`buildschema(`*動態Expr*`)`

**引數**

* *動態 Expr*:將用於聚合計算的運算式。 參數欄型態應`dynamic`為 。 

**傳回**

跨組的*Expr*的最大值。

> [!TIP] 
> 如果`buildschema(json_column)`給出語法錯誤:*是`json_column`字串而不是動態物件?* 如果是這樣,您需要使用`buildschema(parsejson(json_column))`。

**範例**

假設輸入資料行有三個動態值︰

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

* 根物件是具有四個屬性 (名稱分別是 x、y、z 和 t) 的容器。
* 名稱為 "x" 的屬性可以是 "int" 類型或 "string" 類型。
* 名稱為 "y" 的屬性可以是 "double" 類型，或是另一個具有 "w" 屬性且類型為 "string" 的容器。
* ``indexer`` 關鍵字指出 "z" 與 "t" 是陣列。
* "z" 陣列中的每個項目皆為整數或字串。
* "t" 是字串陣列。
* 每個屬性皆可隱含選用，且任何陣列都可以是空的。

### <a name="schema-model"></a>結構描述模型

所傳回結構描述的語法如下︰

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

它們等效於 TypeScript 類型註釋的子集,編碼為 Kusto 動態值。 在 Typescript 中，範例結構描述會是︰

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }