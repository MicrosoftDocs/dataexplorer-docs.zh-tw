---
title: 快取原則（經常性和非經常性快取）-Azure 資料總管
description: 本文說明 Azure 資料總管中的快取原則（經常性存取和非經常性存取快取）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 130526b41030ac3936236f8fd8bba81f20b4bb0e
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512515"
---
# <a name="cache-policy-hot-and-cold-cache"></a>快取原則（經常性存取和非經常性存取快取） 

Azure 資料總管會將其內嵌資料儲存在可靠的儲存體（最常見的 Azure Blob 儲存體），而遠離其實際處理（例如 Azure 計算）節點。 為了加速對該資料的查詢，Azure 資料總管在其處理節點、SSD，甚至是在 RAM 中快取它或部分的元件。 Azure 資料總管包含精密的快取機制，其設計目的是要以智慧方式決定要快取的資料物件。 快取可讓 Azure 資料總管描述其所使用的資料成品，因此更重要的資料可能會優先。 例如，資料行索引和資料行資料分區，

當所有內嵌資料都快取時，就能達到最佳的查詢效能。 有時候，某些資料並不會使其在本機 SSD 儲存體中維持「暖」的成本。
例如，許多小組認為很少存取的舊記錄檔記錄較不重要。
他們想要在查詢此資料時降低效能，而不是付多少錢隨時保持熱。

Azure 資料總管快取提供更細微的快取**原則**，可供客戶用來區別：經常性**資料**快取和**冷資料**快取。 Azure 資料總管快取會嘗試將落在熱資料快取類別中的所有資料，保留在本機 SSD （或 RAM）中，最多可達定義的熱資料快取大小。 剩餘的本機 SSD 空間將用來保存未分類為熱的資料。 這項設計有一個有用的意義，那就是從可靠的儲存體載入大量冷資料的查詢不會從熱資料快取收回資料。 因此，涉及熱資料快取中資料的查詢並不會有重大影響。

設定熱快取原則的主要含意如下：
* **成本**：可靠儲存體的成本可能會明顯低於本機 SSD。 在 Azure 中，目前的成本大約是45倍。
* **效能**：資料在本機 SSD 中的查詢速度較快，特別是針對掃描大量資料的範圍查詢。  

使用 [快取[原則] 命令](cache-policy.md)來管理快取原則。

> [!TIP]
>Azure 資料總管是針對與叢集的總 RAM 具有中繼結果集的臨機操作查詢所設計。
>對於大型作業（例如對應減少），您想要在其中將中繼結果儲存在持續性儲存體（例如 SSD）中，請使用「連續匯出」功能。 這項功能可讓您使用 HDInsight 或 Azure Databricks 等服務，進行長時間執行的批次查詢。
 
## <a name="how-cache-policy-is-applied"></a>如何套用快取原則

將資料內嵌至 Azure 資料總管時，系統會追蹤內嵌的日期和時間，以及所建立的範圍。 範圍的內嵌日期和時間值（如果是從多個預先存在的範圍建立的範圍，則是最大值），用來評估快取原則。

> [!Note]
> 您可以使用 [內嵌] 屬性指定內嵌日期和時間的值 `creationTime` 。

根據預設，有效原則是 `null` ，這表示所有資料都會被視為**熱**。
非 `null` 資料表層級的原則會覆寫資料庫層級原則。

## <a name="scoping-queries-to-hot-cache"></a>將查詢範圍設定為熱快取

Kusto 支援僅限範圍至經常性快取資料的查詢。
有數種查詢可能性。

- 將名為的用戶端要求屬性加入 `query_datascope` 至查詢。
   可能的值： `default` 、 `all` 和 `hotcache` 。
- 在 `set` 查詢文字中使用語句： `set query_datascope='...'` 。
   可能的值與用戶端要求屬性的值相同。
- `datascope=...`在查詢主體中的資料表參考後緊接著加入文字。 
   可能的值是 `all` 和 `hotcache`。

`default`值表示使用叢集預設設定，這會決定查詢應涵蓋所有資料。

如果不同的方法之間有不一致的情況，則 `set` 會優先于用戶端要求屬性。 為數據表參考指定值的優先順序會高於兩者。

例如，在下列查詢中，所有資料表參考只會使用熱快取資料，但 "T" 的第二個參考除外，其範圍限於所有資料：

```kusto
set query_datascope="hotcache";
T | union U | join (T datascope=all | where Timestamp < ago(365d) on X
```

## <a name="cache-policy-vs-retention-policy"></a>快取原則與保留原則的比較

快取原則與[保留原則](./retentionpolicy.md)無關： 
- 快取原則會定義如何設定資源的優先順序。 對重要資料的查詢將會更快，並能抵抗查詢對較不重要資料的影響。
- 保留原則會定義資料表/資料庫中可查詢資料的範圍（尤其是 `SoftDeletePeriod` ）。

設定此原則可根據預期的查詢模式，達到成本與效能之間的最佳平衡。

範例：
* `SoftDeletePeriod`= 56d
* `hot cache policy`= 28d

在此範例中，過去28天的資料會在叢集 SSD 上，而額外28天的資料會儲存在 Azure blob 儲存體中。
您可以在完整56天的資料上執行查詢。
 
