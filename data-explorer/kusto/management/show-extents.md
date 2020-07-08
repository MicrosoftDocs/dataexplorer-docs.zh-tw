---
title: 。顯示範圍-Azure 資料總管
description: 本文說明 Azure 資料總管中的 [顯示範圍] 命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: c0e2ffa76becc747b03b907dfe8a356e4c1a49a3
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060591"
---
# <a name="show-extents"></a>。顯示範圍

顯示指定之資料庫或資料表的範圍。

> [!NOTE]
> 資料分區在 Kusto 中稱為**範圍**，而所有命令都會使用「範圍」或「範圍」做為同義字。
> 如需有關範圍的詳細資訊，請參閱[範圍（資料分區）總覽](extents-overview.md)。

## <a name="cluster-level"></a>叢集層級

`.show` `cluster` `extents` [`hot`]

顯示存在於叢集中的範圍（資料分區）的相關資訊。
如果 `hot` 指定，則只會顯示應該在熱快取中的範圍。

## <a name="database-level"></a>資料庫層級

`.show``database` *DatabaseName* `extents` [ `(` *ExtentId1* `,` ... `,`*ExtentIdN* `)` ][ `hot` ] [ `where` `tags` （ `has` | `contains` | `!has` | `!contains` ） *Tag1* [ `and` `tags` （ `has` | `contains` | `!has` | `!contains` ） *Tag2*...]]

顯示存在於指定資料庫中的範圍（資料分區）的相關資訊。
如果 `hot` 指定，則只會顯示預期在熱快取中的範圍。

## <a name="table-level"></a>資料表層級

`.show``table` *TableName* `extents` [ `(` *ExtentId1* `,` ... `,`*ExtentIdN* `)` ][ `hot` ] [ `where` `tags` （ `has` | `contains` | `!has` | `!contains` ） *Tag1* [ `and` `tags` （ `has` | `contains` | `!has` | `!contains` ） *Tag2*...]]

`.show``tables` `(` *TableName1* `,` ... `,`*TableNameN* `)``extents`[ `(` *ExtentId1* `,` ... `,`*ExtentIdN* `)` ][ `hot` ] [ `where` `tags` （ `has` | `contains` | `!has` | `!contains` ） *Tag1* [ `and` `tags` （ `has` | `contains` | `!has` | `!contains` ） *Tag2*...]]

顯示指定資料表中所存在之範圍（資料分區）的相關資訊。 資料庫取自命令的內容。
如果 `hot` 指定，則只會顯示應該在熱快取中的範圍。

## <a name="notes"></a>備註

* 使用資料庫或資料表層級命令比篩選（新增）叢集 `| where DatabaseName == '...' and TableName == '...'` 層級命令的結果快很多。
* 如果提供了選擇性的範圍識別碼清單，則傳回的資料集會限制為僅限這些範圍。
    * 這個方法比篩選（新增 `| where ExtentId in(...)` 至）「裸機」命令的結果快很多。
* 如果 `tags` 指定了篩選準則：
    * 傳回的清單僅限於標記集合遵守*所有*提供的標記篩選準則的範圍。
    * 這個方法比篩選（新增 `| where Tags has '...' and Tags contains '...'` 至）「裸機」命令的結果快很多。
    * `has`篩選是相等的篩選準則。 未使用任何指定的標記標記的範圍會被篩選掉。
    * `!has`篩選是相等的負篩選。 以其中一個指定的標記標記的範圍會被篩選掉。
    * `contains`篩選是不區分大小寫的子字串篩選準則。 沒有指定字串做為其任何標記之子字串的範圍會被篩選掉。
    * `!contains`篩選是不區分大小寫的子字串負篩選。 具有指定字串做為其任何標記之子字串的範圍會被篩選掉。
  
## <a name="output-parameters"></a>輸出參數

|輸出參數 |類型 |Description |
|---|---|---|
|ExtentId |Guid |範圍的識別碼 
|DatabaseName |String |範圍所屬的資料庫
|TableName |String |範圍所屬的資料表
|MaxCreatedOn |Datetime |建立範圍的日期時間。 針對合併的範圍，在來源範圍中的最大建立時間
|OriginalSize |Double |範圍資料的原始大小（以位元組為單位）
|ExtentSize |Double |記憶體中的範圍大小（已壓縮 + 索引）
|CompressedSize |Double |記憶體中的範圍資料的壓縮大小
|IndexSize |Double |範圍資料的索引大小
|區塊 |long |範圍中的資料區塊數目
|使用者分佈 |long |範圍中的資料區段數目
|AssignedDataNodes |String | 已淘汰（空字串）
|LoadedDataNodes |String |已淘汰（空字串）
|ExtentContainerId |String | 範圍所在的範圍容器識別碼
|RowCount |long |範圍中的資料列數目
|MinCreatedOn |Datetime |建立範圍的日期時間。 針對合併的範圍，在來源範圍中的最小建立時間
|Tags|String|針對範圍定義的標記（如果有的話）
 
## <a name="examples"></a>範例

### <a name="tagged-extent"></a>標記的範圍

`E`資料表中的範圍 `T` 會加上標籤 `aaa` 、和標記 `BBB` `ccc` 。

* 此查詢將會傳回 `E` ：
    
   ```kusto
    .show table T extents where tags has 'aaa' and tags contains 'bb'
   ```
   
* 此查詢不會傳回 `E` ，因為它未標記為 `aa` ：
    
   ```kusto
    .show table T extents where tags has 'aa' and tags contains 'bb'
   ```
    
* 此查詢將會傳回 `E` ：
    
   ```kusto
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
   ```

### <a name="show-volume-of-extents-created"></a>顯示已建立的範圍數量

顯示特定資料庫中每小時所建立的範圍數量

```kusto 
.show database MyDatabase extents | summarize count(ExtentId) by MaxCreatedOn bin=time(1h) | render timechart  
```

### <a name="show-volume-of-data-arriving-by-table-per-hour"></a>顯示每小時到達資料表的資料量

```kusto 
.show database MyDatabase extents  
| summarize sum(OriginalSize) by TableName, MaxCreatedOn bin=time(1h)  
| render timechart
```

### <a name="show-data-size-distribution-by-table"></a>依資料表顯示資料大小散發

```kusto 
.show database MyDatabase extents | summarize sum(OriginalSize) by TableName
```

### <a name="show-all-extents-in-the-database-named-gamesdb"></a>在名為 ' GamesDB ' 的資料庫中顯示所有範圍

```kusto 
.show database GamesDB extents
```

### <a name="show-all-extents-in-the-table-named-games"></a>在名為「遊戲」的資料表中顯示所有範圍

```kusto 
.show table Games extents
```

### <a name="show-all-extents-in-specific-tables"></a>顯示特定資料表中的所有範圍

在名為 ' TaggingGames1 ' 和 ' TaggingGames2 ' 的資料表中顯示所有範圍，同時標記了 ' tag1 ' 和 ' tag2 '

```kusto 
.show tables (TaggingGames1,TaggingGames2) extents where tags has 'tag1' and tags has 'tag2'
```
