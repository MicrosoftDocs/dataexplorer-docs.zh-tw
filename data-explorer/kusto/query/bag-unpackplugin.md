---
title: bag_unpack外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的bag_unpack外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 374ff3ca8101e24a53926a78a1b8781447f32dbc
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518219"
---
# <a name="bag_unpack-plugin"></a>bag_unpack外掛程式

外掛`bag_unpack`程式 通過將每個屬性包頂級外掛槽`dynamic`視為列來 解壓縮單個類型列。

    T | evaluate bag_unpack(col1)

**語法**

*T* `,` `bag_unpack(` T`evaluate`列 =*輸出柱前置文字*:*Column* `|``)`

**引數**

* *T*: 要解壓縮列*柱*的表格輸入。
* *欄*: 要解壓縮的*T*欄位。 其型別必須是 `dynamic`。
* *輸出前置*碼 :要添加到外掛程式生成的所有欄的通用首碼。
  選擇性。

**傳回**

外掛`bag_unpack`程式 傳回具有與其表格輸入 *(T*) 記錄一樣多的表。 表的架構與表格輸入的架構相同,具有以下修改:

* 指定的輸入欄 (*欄*) 將被刪除。

* 架構擴充的欄數量與*T*的頂級屬性包值中有不同的插槽一樣。每列的名稱對應於每個插槽的名稱,可選由*OutputColumnPrefix 預*綴。 其類型可以是槽的類型(如果同一槽的所有值具有相同的類型)或`dynamic`(如果值在類型上不同)。

**注意事項**

外掛程式的輸出架構取決於資料值,因此它與數據本身一樣"不可預測"。 因此,對具有不同數據輸入的外掛程式進行多次執行可能會生成不同的輸出架構。

外掛程式的輸入資料必須如此,以便輸出架構符合表格架構的所有規則。 尤其是：

1. 輸出列名稱不能與表格輸入*T*中的現有欄位,除非它是要解壓縮的欄位(*欄*)本身,因為這將生成兩個具有相同名稱的欄位。

2. 所有槽名稱,當由*OutputColumnPrefix 預*先設定時,必須是有效的實體名稱,並且符合[識別符命名規則](./schema-entities/entity-names.md#identifier-naming-rules)。

**範例**

```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Smitha", "Age":30}),
]
| evaluate bag_unpack(d)
```

|名稱  |Age|
|------|---|
|John  |20 |
|Dave  |40 |
|史密莎|30 |