---
title: Kusto 跨叢集查詢 & 架構變更-Azure 資料總管
description: 本文說明 Azure 資料總管中的跨叢集查詢和架構變更。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9ed8ba6ac5e66326e6aa1d9e7a7f474506b57e26
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328971"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>跨叢集查詢和結構描述變更

執行跨叢集查詢時，執行初始查詢轉譯的叢集必須具有遠端叢集中所參考之實體的架構。
當下列查詢傳送至*Cluster1*時

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*Cluster1*必須知道*Table2* on *Cluster2*的資料行，以及這些資料行的資料類型。 若要取得該資訊，特殊命令會從*Cluster1*傳送至*Cluster2*。
傳送命令可能會很耗費資源。 一旦抓取之後，就會快取遠端實體的架構，而下一個參考相同實體的查詢將會執行較少的網路作業。

遠端實體架構中的變更可能會導致不想要的效果。 例如，無法辨識已加入的資料行，或刪除的資料行導致「部分查詢錯誤」而不是語意錯誤。

若要解決此問題，快取的架構會在抓取之後一小時過期，讓任何查詢在變更之後執行超過一小時，將會使用最新的架構。
