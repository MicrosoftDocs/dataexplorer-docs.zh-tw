---
title: 跨資料庫 & 跨叢集查詢-Azure 資料總管
description: 本文說明 Azure 資料總管中的跨資料庫和跨叢集查詢。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 57b7b6b4c67e0e8903903cef670a561b30b3904e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252578"
---
# <a name="cross-database-and-cross-cluster-queries"></a>跨資料庫與跨叢集查詢

::: zone pivot="azuredataexplorer"

每個 Kusto 查詢都會在目前叢集的內容和預設資料庫中運作。
* 在 [Kusto Explorer](../tools/kusto-explorer.md)中，預設資料庫是在 [連線] [面板](../tools/kusto-explorer.md#connections-panel)中選取的資料庫，而目前的叢集則是包含該資料庫的連接。
* 使用 [用戶端程式庫](../api/netfx/about-kusto-data.md)時，目前的叢集和預設資料庫會分別由 `Data Source` `Initial Catalog` [連接字串](../api/connection-strings/kusto.md)的和屬性指定。

## <a name="queries"></a>查詢
若要從預設值以外的任何資料庫存取資料表，則必須使用 *限定名稱* 語法。

存取目前叢集中的資料庫。

```kusto
database("<database name>").<table name>
```

遠端叢集中的資料庫。
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

*資料庫名稱* 會區分大小寫

叢集*名稱*不區分大小寫，而且可以是下列其中一種形式：
   * 格式正確的 URL，例如 `http://contoso.kusto.windows.net:1234/` 。 僅支援 HTTP 和 HTTPS 架構。
   *  (FQDN) 的完整功能變數名稱，例如 `contoso.kusto.windows.net` 。 這個字串相當於 `https://` **`contoso.kusto.windows.net`** `:443/` 。
   * 沒有網域部分) 的簡短名稱 (主機名稱 [和區域]，例如 `contoso` 或 `contoso.westus` 。 這些字串會轉譯為 `https://` **`contoso`** `.kusto.windows.net:443/` 和 `https://` **`contoso.westus`** `.kusto.windows.net:443/` 。

> [!NOTE]
> 跨資料庫存取會受限於一般許可權檢查。
> 若要執行查詢，您必須具有預設資料庫的 [讀取] 許可權，以及在目前和遠端叢集) 的查詢 (中所參考的每個其他資料庫的 [讀取] 許可權。

*限定名稱* 可用於任何可以使用資料表名稱的內容中。

下列所有動作都有效。

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

當 *限定名稱* 顯示為 [union 運算子](./unionoperator.md)的運算元時，可以使用萬用字元來指定多個資料表和多個資料庫。 叢集名稱中不允許使用萬用字元。

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
> * 預設資料庫的名稱也可能相符，因此資料庫 ( "&#42;" ) 指定所有資料庫的所有資料表（包括預設值）。
> * 如需架構變更如何影響跨叢集查詢的詳細 ionformation，請參閱[跨叢集查詢和架構變更](../concepts/crossclusterandschemachanges.md)。

## <a name="access-restriction"></a>存取限制

限定的名稱或模式也可以包含在 [限制存取](./restrictstatement.md) 語句中，不允許在叢集名稱中使用萬用字元。

```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

上述會將查詢存取限制為下列實體：

* 從預設資料庫中的 [ *我的 ...* ] 開始的任何機構名稱。 
* 目前叢集之所有資料庫中名為 *MyOther* 的資料表。
* 叢集*OtherCluster.kusto.windows.net*中所有名為*my2*的資料庫中的任何資料表。

## <a name="functions-and-views"></a>函數和 Views

函數和 views (持續性和建立的內嵌) 可以跨資料庫和叢集界限參考資料表。 下列程式碼是有效的。

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

您可以從相同叢集中的另一個資料庫存取持續性函數和 views。

表格式函數 (view) `OtherDb` 。

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

中的純量函數 `OtherDb` 。

```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

在 [預設資料庫] 中。

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>跨叢集函式呼叫的限制

表格式函數或 views 可以跨叢集參考。 適用下列限制：

* Remote function 必須傳回表格式架構。 純量函數只能在相同的叢集中存取。
* 遠端函數只能接受純量參數。 取得一或多個資料表引數的函數只能在相同的叢集中存取。
* 遠端函式的架構必須是已知且不變的參數。 如需詳細資訊，請參閱 [跨群集查詢和架構變更](../concepts/crossclusterandschemachanges.md)。

下列跨叢集呼叫是有效的。

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

下列查詢會呼叫遠端純量函數 `MyCalc` 。
此呼叫違反規則 #1，因此無效。

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

下列查詢會呼叫 remote 函數 `MyCalc` ，並提供表格式參數。
此呼叫違反規則 #2，因此無效。

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] )
```

下列查詢會呼叫遠端函式 `SomeTable` ，此函式具有以參數為基礎的變數架構輸出 `tablename` 。
此呼叫違反規則 #3，因此無效。

中的表格式函數 `OtherDb` 。

```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

在 [預設資料庫] 中。

```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

下列查詢會呼叫遠端函式 `GetDataPivot` ，此函式會根據資料 ([pivot ( # A2 外掛程式](pivotplugin.md) 有動態輸出) ，以資料為基礎的變數架構輸出。
此呼叫違反規則 #3，因此無效。

中的表格式函數 `OtherDb` 。

```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

預設資料庫中的表格式函數。

```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>顯示資料

傳回資料給用戶端的語句會隱含地受到傳回的記錄數目的限制，即使沒有任何特定的運算子使用也是一樣 `take` 。 若要提高此限制，請使用 `notruncation` 用戶端要求選項。

若要以圖形形式顯示資料，請使用 [render 運算子](renderoperator.md)。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援跨資料庫和跨叢集查詢。

::: zone-end
