---
title: bag_unpack 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 bag_unpack 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: 48670f1c0bb38599451bc73afa58d6f1dba344a2
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793646"
---
# <a name="bag_unpack-plugin"></a>bag_unpack 外掛程式

外掛程式會將 `bag_unpack` `dynamic` 每個屬性包的最上層位置視為資料行，以解壓縮類型的單一資料行。

```kusto
T | evaluate bag_unpack(col1)
```

## <a name="syntax"></a>語法

*T*資料 `|` `evaluate` `bag_unpack(` *行* [ `,` *OutputColumnPrefix* ] [ `,` *columnsConflict* ] [ `,` *ignoredProperties* ] `)`

## <a name="arguments"></a>引數

* *T*：要解壓縮其 *資料行資料行的* 表格式輸入。
* *Column*：要解壓縮的 *T* 資料行。 其型別必須是 `dynamic`。
* *OutputColumnPrefix*：用來新增至外掛程式所產生之所有資料行的一般前置詞。 此引數是選擇性的。
* *columnsConflict*：資料行衝突解決的方向。 此引數是選擇性的。 當提供引數時，它應該是符合下列其中一個值的字串常值：
    - `error` -查詢會產生 (預設) 的錯誤
    - `replace_source` -已取代來源資料行
    - `keep_source` -保留來源資料行
* *ignoredProperties*：要忽略的選擇性包屬性集合。 當提供引數時，它應該是 `dynamic` 具有一或多個字串常值的陣列常數。

## <a name="returns"></a>傳回

外掛程式會傳回 `bag_unpack` 包含多筆記錄的資料表，做為其表格式輸入 (*T*) 。 資料表的架構與其表格式輸入的架構相同，且具有下列修改：

* 已移除指定的輸入資料行 (資料 *行*) 。
* 架構會以 *T*的最上層屬性包值中的不同位置，以最多的資料行來擴充。每個資料行的名稱會對應到每個位置的名稱，並選擇性地加上 *OutputColumnPrefix*。 如果相同插槽的所有值都有相同的類型，或 `dynamic` 如果類型中的值不同，則其類型為位置的類型。

**備註**

外掛程式的輸出架構取決於資料值，使其成為資料本身的「無法預測」。 使用不同的資料輸入多次執行外掛程式，可能會產生不同的輸出架構。

外掛程式的輸入資料必須是輸出架構遵循表格式架構的所有規則。 尤其是：

* 輸出資料行名稱不能與表格式輸入 *t*中現有的資料行相同，除非它是要 (資料 *行* 解壓縮的資料行) ，因為這會產生兩個具有相同名稱的資料行。

* 所有位置名稱（如果前面加上 *OutputColumnPrefix*）必須是有效的機構名稱，並遵循 [識別碼命名規則](./schema-entities/entity-names.md#identifier-naming-rules)。

## <a name="examples"></a>範例

### <a name="expand-a-bag"></a>展開包


<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d)
```

|Name  |年齡|
|------|---|
|John  |20 |
|Dave  |40 |
|Jasmine|30 |


### <a name="expand-a-bag-with-outputcolumnprefix"></a>使用 OutputColumnPrefix 展開包

展開包，並使用 `OutputColumnPrefix` 選項來產生開頭為前置詞 ' Property_ ' 的資料行名稱。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, 'Property_')
```

|Property_Name|Property_Age|
|---|---|
|John|20|
|Dave|40|
|Jasmine|30|

### <a name="expand-a-bag-with-columnsconflict"></a>使用 columnsConflict 展開包

展開包，並使用 `columnsConflict` 選項來解決運算子所產生之現有資料行和資料行之間的衝突 `bag_unpack()` 。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConflict='replace_source') // Use new name
```

|Name|年齡|
|---|---|
|John|20|
|Dave|40|
|Jasmine|30|

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConflict='keep_source') // Keep old name
```

|Name|年齡|
|---|---|
|Old_name|20|
|Old_name|40|
|Old_name|30|

### <a name="expand-a-bag-with-ignoredproperties"></a>使用 ignoredProperties 展開包

展開包，並使用 `ignoredProperties` 選項來忽略屬性包中的特定屬性。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20, "Address": "Address-1" }),
    dynamic({"Name": "Dave", "Age":40, "Address": "Address-2"}),
    dynamic({"Name": "Jasmine", "Age":30, "Address": "Address-3"}),
]
// Ignore 'Age' and 'Address' properties
| evaluate bag_unpack(d, ignoredProperties=dynamic(['Address', 'Age']))
```

|Name|
|---|
|John|
|Dave|
|Jasmine|
