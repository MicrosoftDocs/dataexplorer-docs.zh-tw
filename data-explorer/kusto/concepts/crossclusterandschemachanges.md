---
title: 跨群集查詢和架構更改 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的跨群集查詢和架構更改。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a9473add657fe8a888818f83c72f12d47138c00e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523285"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>跨叢集查詢和結構描述變更 

執行跨群集查詢時,執行初始查詢解釋的群集必須具有遠端群集中引用的實體的架構。
因此,當以下查詢傳送*到群組 1*時

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*群集 1*必須知道*Cluster2*上的表*2*上有哪些列,以及這些列的數據類型是什麼。 為了完成特殊命令從*群組 1*傳送到群組*2*。
此命令的發送可能非常昂貴,因此一旦檢索,遠端實體的架構將被緩存,引用相同實體的下一個查詢必須執行較少的網路操作。

當遠端實體的架構發生更改(無法識別或刪除列導致"部分查詢錯誤"而不是語義錯誤時,這可能會導致不必要的影響)。

為了緩解此問題,緩存的架構在檢索后 1 小時內過期,因此在更改後 1 小時內執行的任何查詢都將使用最新的架構。