---
title: schema_merge 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 schema_merge 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 2873f3d010464b82ef8cb6a9a3e09f7b0a56b8d9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345703"
---
# <a name="schema_merge-plugin"></a>schema_merge 外掛程式

將表格式架構定義合併成統一的架構。 

架構定義必須是運算子所產生的格式 [`getschema`](./getschemaoperator.md) 。

作業會聯結 `schema merge` 輸入架構中的資料行，並嘗試將資料類型減少為一般資料類型。 如果無法縮減資料類型，就會在有問題的資料行上顯示錯誤。

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

## <a name="syntax"></a>語法

`T``|` `evaluate` `schema_merge(`*PreserveOrder*`)`

## <a name="arguments"></a>引數

* *PreserveOrder*：（選擇性）當設定為時 `true` ，會指示外掛程式驗證資料行順序，如第一個保留的表格式架構所定義。 如果相同的資料行位於數個架構中，則資料行序數必須類似它所出現之第一個架構的資料行序數。 預設值為 `true`。

## <a name="returns"></a>傳回

外掛程式會傳回 `schema_merge` 類似 [`getschema`](./getschemaoperator.md) 運算子傳回的輸出。

## <a name="examples"></a>範例

合併具有附加新資料行的架構。

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

與具有不同資料行順序的架構合併（ `HttpStatus` `1` `2` 在新的 variant 中，序數會從變更為）。

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
|HttpStatus|-1|錯誤（未知的 CSL 類型：錯誤（資料行不按照順序））|錯誤（資料行不按照順序）|

與具有不同資料行順序的架構合併，但 `PreserveOrder` 設定為 `false` 。

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
