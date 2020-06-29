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
ms.openlocfilehash: 9d652ea8551a21d542ad6afef575616e7387183f
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2020
ms.locfileid: "85517881"
---
# <a name="entity-references"></a>實體參考

參考使用其名稱在查詢中 Kusto 架構實體。 有效的機構名稱包含*資料庫*、*資料表*、資料*行*和預存函數。 叢集*無法以*其名稱參考。
如果實體的容器在目前內容中是明確的，請使用機構名稱，而不需要額外的資格。 例如，針對名為的資料庫執行查詢時， `DB` 您可以參考該資料庫中名為的資料表 `T` ，其名稱為 `T` 。

如果無法從內容取得實體的容器，或您想要參考不同于內容中容器的容器中的實體，請使用實體的**限定名稱**。
此名稱會將機構名稱串連至容器的，並可能會將其容器的，依此類推。 如此一來，針對資料庫執行的查詢 `DB` 可能會參考相同叢集 `T1` 之不同資料庫中的資料表 `DB1` ，方法是使用 `database("DB1").T1` 。 如果查詢想要參考另一個叢集的資料表，則可以使用來執行這項操作，例如 `cluster("https://C2.kusto.windows.net/").database("DB2").T2` 。

實體參考也可以使用實體的名稱，前提是它在實體容器的內容中是唯一的。 如需詳細資訊，請參閱[機構名稱](./entity-names.md#entity-pretty-names)。

## <a name="wildcard-matching-for-entity-names"></a>機構名稱的萬用字元比對

在某些內容中，您可以使用萬用字元（ `*` ）來比對機構名稱的全部或部分。 例如，下列查詢會參考目前資料庫中的所有資料表，以及資料庫中 `DB` 名稱開頭為的所有資料表 `T` ：

```kusto
union *, database("DB1").T*
```

> [!NOTE]
> 萬用字元比對不符合以貨幣符號（）開頭的機構名稱 `$` 。
這類名稱是系統保留的。

## <a name="next-steps"></a>後續步驟

* [架構實體類型](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/)
* [架構機構名稱](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names)
