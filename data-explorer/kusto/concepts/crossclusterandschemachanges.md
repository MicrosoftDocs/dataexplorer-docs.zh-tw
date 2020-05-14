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
ms.openlocfilehash: 58fd8cef3836d17eb327734338b1567d815d7349
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382024"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>跨叢集查詢和結構描述變更 

執行跨叢集查詢時，執行初始查詢轉譯的叢集必須具有遠端叢集中所參考之實體的架構。
因此，當下列查詢傳送至*Cluster1*時

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

*Cluster1*必須知道*Table2* on *Cluster2*的資料行，以及這些資料行的資料類型為何。 依照順序完成，特殊命令會從*Cluster1*傳送至*Cluster2*。
此命令的傳送可能相當耗費資源，因此一旦抓取之後，就會快取遠端實體的架構，而下一個參考相同實體的查詢將必須執行較少的網路作業。

當遠端實體的架構變更時（新增的資料行無法辨識或刪除的資料行導致「部分查詢錯誤」而非語意錯誤），這可能會造成不必要的影響。

為了減輕這個問題，快取的架構在抓取後的1小時內過期，因此在變更之後執行的任何查詢都將使用最新的架構。