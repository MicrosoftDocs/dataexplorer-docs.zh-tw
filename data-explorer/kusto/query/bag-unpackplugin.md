---
title: bag_unpack 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 bag_unpack 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: 45dc0a02aae7cc39c7a287036055e9ca447187f3
ms.sourcegitcommit: 085e212fe9d497ee6f9f477dd0d5077f7a3e492e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133454"
---
# <a name="bag_unpack-plugin"></a>bag_unpack 外掛程式

外掛程式會將 `bag_unpack` `dynamic` 每個屬性包的最上層位置視為資料行，以解壓縮類型的單一資料行。

    T | evaluate bag_unpack(col1)

**語法**

*T*資料 `|` `evaluate` `bag_unpack(` *行*[ `,` *OutputColumnPrefix* ] [ `,` *columnsConflict* ] [ `,` *ignoredProperties* ]`)`

**引數**

* *T*：要解壓縮其*資料行的*表格式輸入。
* *Column*：要解除封裝之*T*的資料行。 其型別必須是 `dynamic`。
* *OutputColumnPrefix*：要加入外掛程式所產生之所有資料行的一般前置詞。 此引數是選擇性的。
* *columnsConflict*：資料行衝突解決的方向。 此引數是選擇性的。 當提供引數時，它應該是符合下列其中一個值的字串常值：
    - `error`-Query 會產生錯誤（預設值）
    - `replace_source`-來來源資料行已被取代
    - `keep_source`-來來源資料行已保留
* *ignoredProperties*：要忽略的一組選擇性包屬性。 當提供引數時，它必須是 `dynamic` 具有一或多個字串常值的陣列常數。

**傳回**

外掛程式會傳回 `bag_unpack` 一個資料表，其中包含多筆記錄做為其表格式輸入（*T*）。 資料表的架構與其表格式輸入的架構相同，並具有下列修改：

* 已移除指定的輸入資料行（資料*行*）。
* 架構的延伸包含的資料行數目，與最上層屬性包值*T*中的相異位置不同。每個資料行的名稱都會對應至每個位置的名稱，並選擇性地加上*OutputColumnPrefix*。 如果相同插槽的所有值都具有相同的類型，或如果值的類型不同，則其類型為位置的類型 `dynamic` 。

**注意事項**

外掛程式的輸出架構取決於資料值，使其成為資料本身「無法預測」。 使用不同的資料輸入多次執行外掛程式，可能會產生不同的輸出架構。

外掛程式的輸入資料必須是，輸出架構才會遵循表格式架構的所有規則。 尤其是：

* 輸出資料行名稱不能與表格式輸入*t*中的現有資料行相同，除非它是要解壓縮的資料行（資料*行*），因為這會產生兩個具有相同名稱的資料行。

* 所有位置名稱（在前面加上*OutputColumnPrefix*）必須是有效的機構名稱，並遵循[識別碼命名規則](./schema-entities/entity-names.md#identifier-naming-rules)。

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

|名稱  |Age|
|------|---|
|John  |20 |
|Dave  |40 |
|Jasmine|30 |


### <a name="expand-a-bag-with-outputcolumnprefix"></a>使用 OutputColumnPrefix 擴充包

展開包，並使用 `OutputColumnPrefix` 選項來產生開頭為 ' Property_ ' 前置詞的資料行名稱。

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

### <a name="expand-a-bag-with-columnsconflict"></a>使用 columnsConflict 擴充包

展開包，並使用 `columnsConflict` 選項來解決運算子所產生之現有資料行與資料行之間的衝突 `bag_unpack()` 。

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

|名稱|Age|
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

|名稱|Age|
|---|---|
|Old_name|20|
|Old_name|40|
|Old_name|30|

### <a name="expand-a-bag-with-ignoredproperties"></a>使用 ignoredProperties 擴充包

展開包，並使用 `ignoredProperties` 選項來忽略屬性包中的某些屬性。

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

|名稱|
|---|
|John|
|Dave|
|Jasmine|
