---
title: 跨資料庫和跨群集查詢 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的跨資料庫和跨群集查詢。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8bb0cf2674bae801fb98d14d93518f5617af6807
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766073"
---
# <a name="cross-database-and-cross-cluster-queries"></a>跨資料庫與跨叢集查詢

::: zone pivot="azuredataexplorer"

每個 Kusto 查詢在當前群集和默認資料庫的上下文中運行。
* 在[Kusto 資源管理員](../tools/kusto-explorer.md)中,預設資料庫是[連接面板](../tools/kusto-explorer.md#connections-panel)中選擇的資料庫,目前群集是包含該資料庫的連線
* 使用[Kusto 用戶端庫](../api/netfx/about-kusto-data.md)時,當前群集和默認`Data Source``Initial Catalog`資料庫分別由[Kusto 連接字串](../api/connection-strings/kusto.md)的和 屬性指定。

## <a name="queries"></a>查詢
要從預設資料庫以外的任何資料庫存取表,必須使用*修飾名稱*文法:存取目前群組的資料庫:
```kusto
database("<database name>").<table name>
```
遠端叢集中的資料庫:
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

*資料庫名稱*區分大小寫

*叢集名稱*不區分大小寫,可以是以下形式之一:
* 格式良好的網址:`http://contoso.kusto.windows.net:1234/`例如 ,僅支援 HTTP 和 HTTPS 方案。
* 完全限定的網域名稱 (FQDN):`contoso.kusto.windows.net`例如 - 這將等效於`https://`**`contoso.kusto.windows.net`**`:443/`
* 短名稱 (主機名稱 [和區域] 沒有網域`contoso`部份`https://`**`contoso`**`.kusto.windows.net:443/`):例如`contoso.westus`- 被解釋為 或 - 被解釋為`https://`**`contoso.westus`**`.kusto.windows.net:443/`

> [!NOTE]
> 跨資料庫訪問受通常的許可權檢查。
> 要免除查詢,您必須具有對預設資料庫和查詢中引用的所有其他資料庫(在當前群集和遠端群集中)的讀取許可權。

*限定名稱*可用於可以使用表名稱的任何上下文中。
以下所有操作都有效:

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

當*限定名稱*顯示為[聯合運算符](./unionoperator.md)的操作時,通配符可用於指定多個表和多個資料庫。 叢集名稱不允許使用通配符:

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
>* 默認資料庫的名稱也是可能的匹配,因此資料庫("&#42;")指定所有資料庫的所有表,包括預設值。
>* 架構更改如何影響跨群集查詢,請參閱[跨群集查詢和架構更改](../concepts/crossclusterandschemachanges.md)

## <a name="access-restriction"></a>存取限制 
限定名稱或模式也可以包含在[限制存取](./restrictstatement.md)語句中(不允許在叢集名稱使用通配)
```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

上述內容將限制查詢存取以下主要:

* 預設資料庫中以*我的...* 
* 當前群集的所有資料庫中名為*MyOther*的任何表。
* 群組名稱是*my2...* 的資料庫中的表*OtherCluster.kusto.windows.net*。

## <a name="functions-and-views"></a>功能和檢視

函數和檢視(持久和創建的內聯)可以跨資料庫和群集邊界引用表。 以下有效:

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

可以從同一群集中的另一個資料庫存取持久函數和檢視:

在表`OtherDb`格函數(檢視)

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

中的 Scalar`OtherDb`函數在 中。
```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

在預設資料庫中:

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>跨群集函數呼叫的限制

表格函數或視圖可以跨群集引用。 以下限制適用:

1. 遠端函數必須返回表格架構。 只能在同一群集中訪問 Scalar 函數。
2. 遠端函數只能接受標量參數。 只能在同一群集中訪問獲取一個或多個表參數的函數。
3. 遠端函數的架構必須是已知的,並且其參數不變(另請參閱[跨群集查詢和架構更改](../concepts/crossclusterandschemachanges.md))。

以下跨叢集呼叫**有效**:

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

以下查詢呼叫遠端標量函數`MyCalc`。
這是違反規則#1,因此**無效**:

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

以下查詢調用遠端函數`MyCalc`並提供表格參數。
這是違反規則#2,因此**無效**:

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] ) 
```

以下查詢調用具有基於參數`SomeTable``tablename`的可變架構輸出的遠端函數。
這是違反規則#3,因此**無效**:

中的表格函數在`OtherDb`中:
```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

在預設資料庫中:
```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

以下查詢調用具有基於資料`GetDataPivot`(資料)的可變架構輸出的遠端函數([資料透視()外掛程式](pivotplugin.md)具有動態輸出)。
這是違反規則#3,因此**無效**:

中的表格函數在`OtherDb`中:
```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

預設資料庫中的表格函數:
```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>顯示資料

返回數據的語句受返回的記錄數的隱式限制,即使`take`運算符沒有特定用途也是如此。 若要提高此限制，請使用 `notruncation` 用戶端要求選項。

要顯示圖形顯示資料,請使用[渲染運算子](renderoperator.md)。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
