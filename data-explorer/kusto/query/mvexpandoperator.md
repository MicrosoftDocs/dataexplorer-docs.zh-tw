---
title: mv-展開運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 mv-展開運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: 01a4b56155d1c29727670b4e827cb7b9968ef7a9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512286"
---
# <a name="mv-expand-operator"></a>mv-expand 運算子

展開多值陣組或屬性包。

`mv-expand`應用於[動態](./scalar-data-types/dynamic.md)類型列,以便集合中的每個值獲取單獨的行。 所展開資料列中的其他所有資料行則會重複。 

**語法**

*T* `| mv-expand ` `bag``with_itemindex=``,` *ColumnName**ColumnName**IndexColumnName* =`array`( )= 索引欄位 = 欄位 = 欄位名稱 ... |  `bagexpansion=`【`limit` *行限*】

*T* `| mv-expand ` `array``=``to typeof(``)``)``to typeof(``=`*Name**Name**Typename**Typename**ArrayExpression**ArrayExpression*=`bag` | ( )* 名稱 = 陣列表示式 [ 類型名稱 ] , 名稱 = 陣列表示式 • 類型名稱 * ... `bagexpansion=`【`limit` *行限*】

**引數**

* ** 在結果中，具名資料行中的陣列會展開為多個資料列。 
* ** 產生陣列的運算式。 如果使用這種形式，則會新增新資料行，並保留現有資料行。
* ** 新資料行的名稱。
* *類型名稱:* 指示陣列元素的基礎類型,該類型將成為運算符生成的列的類型。
    請注意,陣列中不符合此類型的值將不會轉換;因此,將不轉換與此類型不一致的值。相反,他們將承擔一個`null`價值。
* ** 從每個原始資料列所產生的資料列數目上限。 默認值為 2147483647。 
*注意*:運算符`mvexpand`的舊體和過時形式預設行限制為 128。
* *索引欄位:* 如果`with_itemindex`指定,輸出將包含附加列(名為*IndexColumnName),* 該列包含原始展開集合中項的索引(從 0 開始)。 

**傳回**

具名資料行的任何陣列中或是陣列運算式中的每個值會佔據多個資料列。
如果指定了多個列或表達式,它們將並行展開,因此對於每個輸入行,輸出行的輸出行數將與最長展開表達式中的元素相同(較短的清單用null填充)。 如果行中的值是空陣組,則該行將展開為無(不會在結果集中顯示)。 如果行中的值不是陣列,則該行將保持結果集中的保留狀態。 

展開的資料行一律具有動態類型。 如果您想要計算或彙總多個值，請使用轉換函式，例如 `todatetime()` 或 `tolong()`。

有兩種屬性包展開模式可受到支援︰
* `bagexpansion=bag`︰屬性包會展開為單一項目屬性包。 這是預設展開模式。
* `bagexpansion=array`:屬性包被擴展為雙元素`[`*鍵*`,`*值*`]`數位結構,允許統一訪問鍵和值(例如,對屬性名稱運行不同計數聚合)。 

**範例**

單欄的簡單延伸:
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|{"道具1":"a"|
|1|{"prop2":"b"}|


展開兩列將首先「壓縮」適用的列,然後展開它們:

```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b, c 
```

|a|b|c|
|---|---|---|
|1|{"道具1":"a"|5|
|1|{"prop2":"b"}||

如果要獲取擴展兩列的笛卡爾產品,則逐一展開:
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b 
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|{"道具1":"a"|5|
|1|{"prop2":"b"}|5|


使用的陣列延伸`with_itemindex`:
```kusto
range x from 1 to 4 step 1 
| summarize x = make_list(x) 
| mv-expand with_itemindex=Index  x 
```

|x|索引|
|---|---|
|1|0|
|2|1|
|3|2|
|4|3|


**更多範例**

請參考[此時間時即時活動的圖表計數](./samples.md#concurrent-activities)。

**另請參閱**

- [mv 應用](./mv-applyoperator.md)運算符。
- [匯總執行相反功能的make_list()。](makelist-aggfunction.md)
- [bag_unpack()](bag-unpackplugin.md)外掛程式,用於使用屬性包鍵將動態 JSON 物件擴展到列中。