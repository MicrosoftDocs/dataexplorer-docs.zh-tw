---
title: 尋找運算子 ─ Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的查找運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 1325a1b839b62baacade4cf7c64cd278aeeabaf5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513051"
---
# <a name="lookup-operator"></a>lookup 運算子

運算子`lookup`延伸事實資料表的列,並在維度表中查看值。

```kusto
FactTable | lookup kind=leftouter (DimensionTable) on CommonColumn, $left.Col1 == $right.Col2
```

在這裡,結果是一個表,`FactTable``$left``DimensionTable`透過執行從前一個表中尋找每對`$right`(`CommonColumn``Col`的 ) 的資料(引用) 的表,其中`CommonColumn1``Col2`每對 (,) 在後一個表中每對 ( , ) 。 有關事實表和維度表之間的差異,請參閱[事實和維度表](../concepts/fact-and-dimension-tables.md)。 

操作員`lookup`執行類似於[聯接運算子](joinoperator.md)的操作,具有以下差異:

* 結果不會重複`$right`表中作為聯接操作基礎的列。
* 只支援兩種搜尋,`leftouter`並且`inner``leftouter`為 預設值。
* 在性能方面,默認情況下,系統假定`$left`表是較大的(事實)表`$right`, 表是較小的(維度)表。 這與`join`運算符使用的假設完全相反。
* 操作員`lookup`會自動`$right`將 表`$left`廣播到 表(基本上,行為行為就像指定了一`hint.broadcast`樣)。 請注意,這限制了`$right`表的大小。

**語法**

*左表*`|``lookup``leftouter``(``)``on`*Attributes*=`inner`( )]*右表*屬性|`kind``=`

**引數**

* *左表*:作為查找基礎的表或表格運算式。
  表示為`$left`。

* *右表*:用於「填充」事實表中的新列的表或表格運算式。 表示為`$right`。

* *屬性*:一個或多個規則的逗號分隔清單,用於描述*左表*的行如何與*右表*的行匹配。 使用`and`邏輯運算符評估多個規則。
  規則可以是:

  |規則類型        |語法                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |依名稱相等 |*ColumnName*                                    |`where`*左表*。*欄位*`==`*右表*。*欄名稱*|
  |依值相等|`$left.`*左柱*`==``$right.`*右柱*|`where``$left.`*LeftColumn*左`==`柱`$right.`=右列        |

  > [!Note] 
  > 在「按值相等」 的情況下,*欄位名稱必須*限定為由`$left`和`$right`符號表示的適用擁有者表。

* `kind`:有關如何處理*左表中*在*右表*中不匹配的行的可選說明。 預設情況下,`leftouter`使用,這意味著所有這些行將顯示在輸出中,其中空值用於運算符添加的*RightTable*列的缺失值。 如果使用`inner`,則輸出中省略此類行。 `looku`(p 運算符不支援其他類型的聯接。
  
**傳回**

含有下列項目的資料表︰

* 兩個資料表中的每個資料行各自佔據一個資料行，包括用來比對的索引鍵。
  如果存在名稱衝突,右側的列將自動重新命名。
* 輸入資料表間的每個相符項目各自佔據一個資料列。 其中一個資料表中選取的資料列如果和另一個資料表中的資料列在所有 `on` 欄位的值皆相同，就代表是相符項目。 
* 屬性(尋找鍵)將在輸出表中只顯示一次。

 * `kind`未指定,`kind=leftouter`

     除了內部的相符項目，左側 (和/或右側) 的每個資料列即使不相符也會佔有一個資料列。 在此情況下，不相符的輸出資料格會包含 null。

 * `kind=inner`

     左側和右側相符資料列的每個組合都會在輸出中佔有一個資料列。

**範例**

```kusto
let FactTable=datatable(Row:string,Personal:string,Family:string) [
  "1", "Bill",   "Gates",
  "2", "Bill",   "Clinton",
  "3", "Bill",   "Clinton",
  "4", "Steve",  "Ballmer",
  "5", "Tim",    "Cook"
];
let DimTable=datatable(Personal:string,Family:string,Alias:string) [
  "Bill",  "Gates",   "billg",
  "Bill",  "Clinton", "billc",
  "Steve", "Ballmer", "steveb",
  "Tim",   "Cook",    "timc"
];
FactTable
| lookup kind=leftouter DimTable on Personal, Family
```

資料列     | Personal  | 系列   | Alias
--------|-----------|----------|--------
1       | Bill      | 蓋茨    | 比爾格
2       | Bill      | 克林頓  | 比爾克
3       | Bill      | 克林頓  | 比爾克
4       | Steve     | 鮑爾默  | 史蒂夫布
5       | 蒂姆       | 廚師     | 蒂姆克