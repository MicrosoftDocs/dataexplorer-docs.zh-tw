---
title: 跨資料庫與跨叢集查詢-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的跨資料庫和跨叢集查詢。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: eaf42247840bfc5446c61bcefbb205c9e49706c3
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737737"
---
# <a name="cross-database-and-cross-cluster-queries"></a>跨資料庫與跨叢集查詢

::: zone pivot="azuredataexplorer"

每個 Kusto 查詢都會在目前叢集和預設資料庫的內容中運作。
* 在[Kusto Explorer](../tools/kusto-explorer.md)中，預設資料庫是在 [連線][面板](../tools/kusto-explorer.md#connections-panel)中選取的資料庫，而目前的叢集是包含該資料庫的連接
* 使用[Kusto 用戶端程式庫](../api/netfx/about-kusto-data.md)時，目前的叢集和預設資料庫會分別`Data Source`由`Initial Catalog` [Kusto 連接字串](../api/connection-strings/kusto.md)的和屬性指定。

## <a name="queries"></a>查詢
若要從預設值以外的任何資料庫存取資料表，必須使用*限定名稱*語法：若要存取目前叢集中的資料庫：
```kusto
database("<database name>").<table name>
```
遠端叢集中的資料庫：
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

*資料庫名稱*區分大小寫

叢集*名稱*不區分大小寫，而且可以是下列其中一種形式：
* 格式正確的 URL：範例`http://contoso.kusto.windows.net:1234/`，僅支援 HTTP 和 HTTPS 架構。
* 完整功能變數名稱（FQDN）：例如`contoso.kusto.windows.net` ，其將相當於`https://`**`contoso.kusto.windows.net`**`:443/`
* 簡短名稱（不含網域部分的主機名稱 [和區域]）：例如`contoso` `https://` **`contoso`** `.kusto.windows.net:443/`，其會解讀為、或`contoso.westus` -其會解讀為`https://`**`contoso.westus`**`.kusto.windows.net:443/`

> [!NOTE]
> 跨資料庫存取權受限於一般許可權檢查。
> 若要 excute 查詢，您必須擁有預設資料庫的 [讀取] 許可權，以及查詢中所參考的每個其他資料庫（在目前和遠端叢集中）。

*限定名稱*可用於任何可以使用資料表名稱的內容中。
下列全部都是有效的：

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

當*限定名稱*顯示為聯[集運算子](./unionoperator.md)的運算元時，可以使用萬用字元來指定多個資料表和多個資料庫。 叢集名稱中不允許使用萬用字元：

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
>* 預設資料庫的名稱也是可能的相符項，因此資料庫（"&#42;"）。 * 會指定所有資料庫的所有資料表，包括預設值。
>* 架構變更如何影響跨叢集查詢，請參閱[跨叢集查詢和架構變更](../concepts/crossclusterandschemachanges.md)

## <a name="access-restriction"></a>存取限制 
限定的名稱或模式也可以包含在[限制存取](./restrictstatement.md)語句中（叢集名稱中不允許使用萬用字元）
```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

上述的會將查詢存取限制為下列實體：

* 預設資料庫中以*my ...* 開頭的任何機構名稱。 
* 目前叢集的所有資料庫中的任何資料表，名為*MyOther ...* 。
* 叢集*OtherCluster.kusto.windows.net*中所有名為*my2*的資料庫中的任何資料表。

## <a name="functions-and-views"></a>函數和觀點

函數和觀點（持續性和建立的內嵌）可以跨資料庫和叢集界限參考資料表。 以下是有效的：

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

持續性函數和 views 可以從相同叢集中的另一個資料庫存取：

表格式函數（view）中`OtherDb`的：

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

中的`OtherDb`純量函數：
```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

在 [預設資料庫] 中：

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>跨叢集函數呼叫的限制

表格式函數或 views 可以跨叢集參考。 適用下列限制：

1. 遠端函式必須傳回表格式架構。 純量函數只能在相同的叢集中存取。
2. 遠端函數只能接受純量參數。 取得一或多個資料表引數的函式只能在相同的叢集中存取。
3. 遠端函式的架構必須是已知且不變的參數（請參閱[跨叢集查詢和架構變更](../concepts/crossclusterandschemachanges.md)）。

下列跨叢集呼叫**有效**：

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

下列查詢會呼叫遠端純量`MyCalc`函數。
這**違反了規則**#1，因此無效：

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

下列查詢會呼叫遠端函數`MyCalc` ，並提供表格式參數。
這**違反了規則**#2，因此無效：

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] ) 
```

下列查詢會呼叫具有以`SomeTable`參數`tablename`為基礎之變數架構輸出的遠端函數。
這**違反了規則**#3，因此無效：

中的`OtherDb`表格式函數：
```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

在 [預設資料庫] 中：
```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

下列查詢會呼叫具有以`GetDataPivot`資料為基礎之變數架構輸出的遠端函數（[pivot （）外掛程式](pivotplugin.md)具有動態輸出）。
這**違反了規則**#3，因此無效：

中的`OtherDb`表格式函數：
```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

預設資料庫中的表格式函數：
```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>顯示資料

將資料傳回給用戶端的語句會隱含地受傳回的記錄數目限制，即使沒有特定的`take`運算子用法也一樣。 若要提高此限制，請使用 `notruncation` 用戶端要求選項。

若要以圖形形式顯示資料，請使用[render 運算子](renderoperator.md)。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
