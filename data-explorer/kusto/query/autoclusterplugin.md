---
title: autocluster 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 autocluster 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 86e3ce4f1cbb957ebd126a8493ebb6b7bc5ac66b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349409"
---
# <a name="autocluster-plugin"></a>autocluster 外掛程式

```kusto
T | evaluate autocluster()
```

`autocluster`尋找資料中離散屬性（維度）的一般模式。 然後，它會將原始查詢的結果（不論是100還是10萬個數據列）縮減成少量的模式。 外掛程式的開發目的是要協助分析失敗（例如例外狀況或當機），但可能會對任何篩選的資料集進行處理。

## <a name="syntax"></a>語法

`T | evaluate autocluster(`*引數*`)`

## <a name="returns"></a>傳回

外掛程式會傳回 `autocluster` 一組（通常是一小部分）的模式。 模式會在多個離散屬性中，使用共用的通用值來捕捉部分資料。 結果中的每個模式都會以一列來表示。

第一個資料行是區段識別碼。 下兩個資料行是原始查詢中由模式所擷取之資料列的計數和百分比。 其餘的資料行是來自原始查詢。 其值是資料行中的特定值，或表示變數值的萬用字元值（預設為 null）。

模式不是相異的、可能會重迭，而且通常不會涵蓋所有原始資料列。 某些資料列可能不會落在任何模式之下。

> [!TIP]
> 在輸入管道中使用 where 和[project](./projectoperator.md) ，將資料減少到您感興趣的[位置](./whereoperator.md)。
>
> 當您找到有趣的資料列時，您可藉由將其特定值加入至您的 `where` 篩選器，進一步深入探索。

**引數 (全部選用)**

`T | evaluate autocluster(`[*SizeWeight*， *WeightColumn*， *NumSeeds*， *CustomWildcard*， *CustomWildcard*，...]`)`

所有引數皆為選用，但必須為上述順序。 若要指出應該使用預設值，請放置字串波狀符號值 ' ~ ' （請參閱資料表中的「範例」資料行）。

|引數        | 類型、範圍、預設值              |描述                | 範例                                        |
|----------------|-----------------------------------|---------------------------|------------------------------------------------|
| SizeWeight     | 0 < *double* < 1 [預設值： 0.5]   | 可讓您控制泛型（高涵蓋範圍）與資訊（多個共用）值之間的平衡。 如果您增加此值，通常會減少模式數目，而每個模式傾向于涵蓋較大的百分比涵蓋範圍。 如果您降低此值，通常會產生具有更多共用值和較小涵蓋範圍的更特定模式。 「內部」公式是加權幾何平均值，介於標準化的一般分數和具有權數的資訊分數 `SizeWeight` 之間，`1-SizeWeight`                   | `T | evaluate autocluster(0.8)`                |
|WeightColumn    | *column_name*                     | 根據指定的權數 (依預設每個資料都列具有權數 '1') 考慮輸入中的每個資料列。 引數必須是數值資料行的名稱（例如 int、long、real）。 權數資料行的常見用法是考慮已內嵌至各資料列的資料取樣或分組/彙總。                                                                                                       | `T | evaluate autocluster('~', sample_Count)` | 
| NumSeeds        | *int* [預設值： 25]              | 種子數目可決定演算法的初始本機搜尋點數。 在某些情況下，視資料的結構而定，如果您增加種子的數目，則結果的數目（或品質）會隨著查詢取捨較慢的擴展搜尋空間而增加。 此值會以兩個方向減少結果，因此，如果您將它降到以下五個，它將可達到不明顯的效能改進。 如果您增加到50以上，很少會產生其他模式。                                         | `T | evaluate autocluster('~', '~', 15)`       |
| CustomWildcard  | *"any_value_per_type"*           | 設定結果資料表中特定類型的萬用字元值。 它會指出目前的模式對此資料行沒有限制。 預設值為 null，因為字串預設值為空字串。 如果預設值是資料中的良好值，則應該使用不同的萬用字元值（例如 `*` ）。                                                                                                                | `T | evaluate autocluster('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))` |

## <a name="examples"></a>範例

### <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage
| evaluate autocluster(0.6)
```

|SegmentId|計數|百分比|State|EventType|Damage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38.7||Hail|否
|1|512|8.7||Thunderstorm Wind|YES
|2|898|15.3|德克薩斯州||

### <a name="example-with-custom-wildcards"></a>包含自訂萬用字元的範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage 
| evaluate autocluster(0.2, '~', '~', '*')
```

|SegmentId|計數|百分比|State|EventType|Damage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38.7|\*|Hail|否
|1|512|8.7|\*|Thunderstorm Wind|YES
|2|898|15.3|德克薩斯州|\*|\*

## <a name="see-also"></a>另請參閱

* [basket](./basketplugin.md)
* [減少](./reduceoperator.md)

* `autocluster`主要是根據下列檔中的種子展開演算法：[使用離散屬性的遙測資料採礦演算法](https://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1)。 
