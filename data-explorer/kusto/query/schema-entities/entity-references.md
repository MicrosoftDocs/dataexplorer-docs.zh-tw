---
title: 實體參考-Azure 資料總管
description: 本文說明 Azure 資料總管中的實體參考。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fc36f31bcdb90ed270a4ad21874d121d91fe429e
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342683"
---
# <a name="entity-references"></a>實體參考

參考在查詢中使用名稱 Kusto 架構實體。 有效的機構名稱包括 *資料庫*、 *資料表*、資料 *行*及預存函數。 叢集*無法依*名稱參考。
如果實體的容器在目前的內容中是明確的，請使用機構名稱，而不需要額外的資格。 例如，針對名為的資料庫執行查詢時， `DB` 您可以參考 `T` 資料庫名稱中所呼叫的資料表 `T` 。

如果實體的容器無法在內容中使用，或您想要從與內容中容器不同的容器參考實體，請使用實體的 **限定名稱**。
名稱會將機構名稱串連到容器的，也可能是其容器的等等。 如此一來，針對資料庫執行的查詢 `DB` 可能會參考相同叢集 `T1` 之不同資料庫中的資料表 `DB1` ，方法是使用 `database("DB1").T1` 。 如果查詢想要參考另一個叢集中的資料表，則可以使用來進行參考 `cluster("https://C2.kusto.windows.net/").database("DB2").T2` 。

實體參考也可以使用實體的名稱，但前提是它在實體容器的內容中是唯一的。 如需詳細資訊，請參閱 [實體的名稱](./entity-names.md#entity-pretty-names)。

## <a name="wildcard-matching-for-entity-names"></a>機構名稱的萬用字元比對

在某些內容中，您可以使用萬用字元 (`*`) ，以符合所有或部分的機構名稱。 例如，下列查詢會參考目前資料庫中的所有資料表，以及資料庫中 `DB` 名稱開頭為的所有資料表 `T` ：

```kusto
union *, database("DB1").T*
```

> [!NOTE]
> 萬用字元比對不符合以貨幣符號 () 開頭的機構名稱 `$` 。
這類名稱是系統保留的名稱。

## <a name="next-steps"></a>後續步驟

* [架構實體類型](./index.md)
* [架構機構名稱](./entity-names.md)