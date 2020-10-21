---
title: 查閱運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的查閱運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 3305d78dd903160491ac3cdabd274ce8ace8ba2f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246421"
---
# <a name="lookup-operator"></a>lookup 運算子

`lookup`運算子會擴充事實資料表的資料行，以及在維度資料表中查閱的值。

```kusto
FactTable | lookup kind=leftouter (DimensionTable) on CommonColumn, $left.Col1 == $right.Col2
```

在這裡，結果是一個資料表，它會 `FactTable` 藉 `$left` `DimensionTable` 由執行每一組 (的查閱，以) 所參考的 (資料來擴充 () ，) 從前者的資料表中， (第 `$right` `CommonColumn` `Col` 二個配對) ，在第一個 `CommonColumn1` `Col2` 資料表中。 如需事實和維度資料表之間的差異，請參閱 [事實和維度資料表](../concepts/fact-and-dimension-tables.md)。 

`lookup`運算子會執行類似于[聯結運算子](joinoperator.md)的作業，但有下列差異：

* 結果不會針對聯結作業的基礎，從資料表中重復資料行 `$right` 。
* 僅支援兩種查閱， `leftouter` 以及 `inner` `leftouter` 預設值。
* 就效能而言，系統預設 `$left` 會假設資料表是) 資料表中較大的 (事實，而 `$right` 資料表是) 資料表中較小的 (維度。 這與運算子所使用的假設完全相反 `join` 。
* `lookup`運算子會自動將 `$right` 資料表廣播給 `$left` 資料表 (基本上，其行為就如同 `hint.broadcast`) 指定。 請注意，這會限制資料表的大小 `$right` 。

## <a name="syntax"></a>語法

*LeftTable* `|``lookup`[ `kind` `=` (`leftouter` | `inner`) ] `(` *RightTable* `)` `on` *屬性*

## <a name="arguments"></a>引數

* *LeftTable*：做為查閱基礎的資料表或表格式運算式。
  表示為 `$left` 。

* *RightTable*：用來「填入」事實資料表中新資料行的資料表或表格式運算式。 表示為 `$right` 。

* *屬性*：一或多個規則的逗號分隔清單，描述 *LeftTable* 的資料列如何與 *RightTable*中的資料列進行比對。 使用邏輯運算子來評估多個規則 `and` 。
  規則可以是下列其中一個：

  |規則種類        |語法                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |依名稱的相等 |*ColumnName*                                    |`where`*LeftTable*。*ColumnName* `==`*RightTable*。*ColumnName*|
  |依值的相等|`$left.`*LeftColumn* `==``$right.` *RightColumn*|`where``$left.` *LeftColumn* `==` LeftColumn `$right.`*RightColumn        |

  > [!Note] 
  > 如果是「依值的相等」，則資料行名稱 *必須* 使用以和標記法表示的適用擁有者資料表加以限定 `$left` `$right` 。

* `kind`：有關如何在 *LeftTable* 中處理在 *RightTable*中沒有相符之資料列的選擇性指示。 根據預設， `leftouter` 會使用，這表示所有這些資料列都會出現在輸出中，並將 null 值用於運算子所新增之 *RightTable* 資料行的遺漏值。 如果 `inner` 使用，則會從輸出中省略這類資料列。 P 運算子不支援其他類型的聯結 (`looku` 。 ) 
  
## <a name="returns"></a>傳回

含有下列項目的資料表︰

* 兩個資料表中的每個資料行各自佔據一個資料行，包括用來比對的索引鍵。
  如果發生名稱衝突，就會自動重新命名右邊的資料行。
* 輸入資料表間的每個相符項目各自佔據一個資料列。 其中一個資料表中選取的資料列如果和另一個資料表中的資料列在所有 `on` 欄位的值皆相同，就代表是相符項目。 
*  (查閱索引鍵的屬性) 只會在輸出資料表中出現一次。

 * `kind` 未知 `kind=leftouter`

     除了內部的相符項目，左側 (和/或右側) 的每個資料列即使不相符也會佔有一個資料列。 在此情況下，不相符的輸出資料格會包含 null。

 * `kind=inner`

     左側和右側相符資料列的每個組合都會在輸出中佔有一個資料列。

## <a name="examples"></a>範例

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

資料列     | 個人  | 系列   | Alias
--------|-----------|----------|--------
1       | Bill      | 蓋茨    | billg
2       | Bill      | 克林頓  | billc
3       | Bill      | 克林頓  | billc
4       | Steve     | 鮑爾默  | steveb
5       | 蒂姆       | 廚師     | timc