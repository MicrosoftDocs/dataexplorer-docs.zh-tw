---
title: autocluster 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 autocluster 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a7caa8935176b2d4d52bf8955262509bb2069dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248056"
---
# <a name="autocluster-plugin"></a>autocluster 外掛程式

```kusto
T | evaluate autocluster()
```

`autocluster` 尋找資料中)  (維度的離散屬性通用模式。 然後，它會將原始查詢的結果（不論是100或100k 資料列）縮減為少量的模式。 此外掛程式的開發目的是協助分析失敗 (例如例外狀況或損毀) ，但可能會處理任何已篩選的資料集。

> [!NOTE]
> `autocluster` 主要是以下列檔中的 Seed-Expand 演算法為基礎： [使用離散屬性的遙測資料採礦演算法](https://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1)。 


## <a name="syntax"></a>語法

`T | evaluate autocluster(`*引數*`)`

## <a name="returns"></a>傳回

外掛程式會傳回 `autocluster` 一組 (，通常是一組小) 的模式。 模式會在多個離散屬性之間，以共用的通用值來捕捉部分資料。 結果中的每個模式都會以資料清單示。

第一個資料行是區段識別碼。 下兩個資料行是原始查詢中由模式所擷取之資料列的計數和百分比。 其餘的資料行是來自原始查詢。 其值是資料行中的特定值，或萬用字元值 (預設為 null) 表示變數值。

這些模式不相異、可能會重迭，而且通常不會涵蓋所有原始資料列。 某些資料列可能不會落在任何模式之下。

> [!TIP]
> 在輸入管道中使用 [where](./whereoperator.md) 和 [project](./projectoperator.md) ，將資料縮減為您感興趣的內容。
>
> 當您找到有趣的資料列時，您可藉由將其特定值加入至您的 `where` 篩選器，進一步深入探索。

## <a name="arguments"></a>引數 

> [!NOTE] 
> 所有引數都是選擇性的。

`T | evaluate autocluster(`[*SizeWeight*， *WeightColumn*， *NumSeeds*， *CustomWildcard*， *CustomWildcard*，...]`)`

所有引數皆為選用，但必須為上述順序。 若要指出應使用預設值，請將字串波狀符號值 ' ~ ' (查看資料表) 中的 "Example" 資料行。

|引數        | Type、range、default              |描述                | 範例                                        |
|----------------|-----------------------------------|---------------------------|------------------------------------------------|
| SizeWeight     | 0 < *雙精度* < 1 [預設值： 0.5]   | 讓您可以控制泛型 (高涵蓋範圍) 和資訊 (許多共用) 值之間的平衡。 如果您增加值，通常會減少模式數目，而每個模式傾向于涵蓋較大的百分比涵蓋範圍。 如果您降低值，通常會產生更多具有更多共用值的特定模式，並提供較小的百分比範圍。 幕後的公式是加權幾何平均值，介於正規化的泛型分數與具有加權的資訊分數之間 `SizeWeight``1-SizeWeight`                   | `T | evaluate autocluster(0.8)`                |
|WeightColumn    | *column_name*                     | 根據指定的權數 (依預設每個資料都列具有權數 '1') 考慮輸入中的每個資料列。 引數必須是數值資料行的名稱 (例如 int、long、real) 。 權數資料行的常見用法是考慮已內嵌至各資料列的資料取樣或分組/彙總。                                                                                                       | `T | evaluate autocluster('~', sample_Count)` | 
| NumSeeds        | *int* [default： 25]              | 種子數目可決定演算法的初始本機搜尋點數。 在某些情況下，視資料的結構而定，如果您增加種子的數目，則結果的 (或品質) 數量會隨著查詢取捨較慢的擴展搜尋空間而增加。 這兩個方向的值都有降低的結果，因此，如果您將它減少到低於五個，將可達到明顯的效能改進。 如果您增加到50以上，很少會產生其他模式。                                         | `T | evaluate autocluster('~', '~', 15)`       |
| CustomWildcard  | *"any_value_per_type"*           | 設定結果資料表中特定類型的萬用字元值。 它會指出目前的模式對此資料行沒有限制。 預設值為 null，因為字串預設值為空字串。 如果預設值在資料中是很好的值，則應該使用不同的萬用字元值 (例如 `*`) 。                                                                                                                | `T | evaluate autocluster('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))` |

## <a name="examples"></a>範例

### <a name="using-autocluster"></a>使用 autocluster

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage
| evaluate autocluster(0.6)
```

|SegmentId|計數|百分比|狀態|EventType|Damage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38.7||Hail|否
|1|512|8.7||Thunderstorm Wind|YES
|2|898|15.3|德克薩斯州||

### <a name="using-custom-wildcards"></a>使用自訂萬用字元

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage 
| evaluate autocluster(0.2, '~', '~', '*')
```

|SegmentId|計數|百分比|狀態|EventType|Damage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38.7|\*|Hail|否
|1|512|8.7|\*|Thunderstorm Wind|YES
|2|898|15.3|德克薩斯州|\*|\*

## <a name="see-also"></a>另請參閱

* [basket](./basketplugin.md)
* [減少](./reduceoperator.md)
