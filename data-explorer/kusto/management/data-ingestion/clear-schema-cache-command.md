---
title: 清除用於串流內嵌的快取架構-Azure 資料總管
description: 本文說明在 Azure 資料總管中清除快取資料庫架構的管理命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/20/2020
ms.openlocfilehash: 23d86e528dfe687b79ce225b16712184a9bfcde4
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784492"
---
# <a name="clear-schema-cache-for-streaming-ingestion"></a>清除用於串流內嵌的架構快取

叢集節點會快取透過串流內嵌接收資料之資料庫的架構。 此程式可優化叢集資源的效能和使用，但可能會在架構變更時造成傳播延遲。
清除快取，以確保後續的串流內嵌要求會併入資料庫或資料表架構變更。
如需詳細資訊，請參閱[串流內嵌和架構變更](streaming-ingestion-schema-changes.md)。

**清除架構快取**

`.clear cache streamingingestion schema`命令會從所有叢集節點中清除快取的架構。

**語法**

`.clear``table` &lt; &gt; 資料表 `cache` 名稱 `streamingingestion``schema`

`.clear` `database` `cache` `streamingingestion` `schema`

**傳回**

此命令會傳回具有下列資料行的資料表：

|資料行    |類型    |描述
|---|---|---
|NodeId|`string`|叢集節點的識別碼
|狀態|`string`|成功/失敗

**範例**

```kusto
.clear database cache streamingingestion schema

.show table T1 cache streamingingestion schema
```

|NodeId|狀態|
|---|---|
|Node1|成功
|Node2|Failed

> [!NOTE]
> 如果命令失敗，或傳回資料表中的其中一個資料列包含*Status = Failed* ，則可以安全地重試命令。
