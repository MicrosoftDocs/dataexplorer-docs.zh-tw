---
title: schema_merge 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 schema_merge 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 9a6f25a7ea1c75211d043fdbca7f99c16ff98e1b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250339"
---
# <a name="schema_merge-plugin"></a>schema_merge 外掛程式

將表格式架構定義合併成統一的架構。 

架構定義的格式應為運算子所產生的格式 [`getschema`](./getschemaoperator.md) 。

作業會聯結 `schema merge` 輸入架構中的資料行，並嘗試將資料類型減少為一般資料類型。 如果無法減少資料類型，就會在有問題的資料行上顯示錯誤。

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

## <a name="syntax"></a>語法

`T``|` `evaluate` `schema_merge(`*PreserveOrder*`)`

## <a name="arguments"></a>引數

* *PreserveOrder*： (選擇性) 設定為時 `true` ，會指示外掛程式驗證資料行順序，如第一個保留的表格式架構所定義。 如果相同的資料行是在多個架構中，則資料行序數必須像出現在中的第一個架構的資料行序數。 預設值為 `true`。

## <a name="returns"></a>傳回

外掛程式會傳回 `schema_merge` 類似于運算子傳回 [`getschema`](./getschemaoperator.md) 的輸出。

## <a name="examples"></a>範例

與已附加新資料行的架構合併。

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, HttpStatus:int, Referrer:string)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*結果*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|字串|
|HttpStatus|1|System.Int32|int|
|Referrer|2|System.String|字串|

`HttpStatus` `1` 在新的 variant) 中，與具有不同資料行排序 (序數變更的架構合併 `2` 。

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*結果*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|字串|
|Referrer|1|System.String|字串|
|HttpStatus|-1| (未知的 CSL 類型時發生錯誤： (資料行順序錯誤) # A3|錯誤 (資料行順序不) |

與具有不同資料行排序的架構合併，但 `PreserveOrder` 設定為 `false` 。

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge(PreserveOrder = false)
```

*結果*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|字串
|Referrer|1|System.String|字串
|HttpStatus|2|System.Int32|int|
