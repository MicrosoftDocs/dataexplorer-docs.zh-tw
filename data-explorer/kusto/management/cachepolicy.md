---
title: 快取策略(冷熱緩存) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的緩存策略(冷緩存)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 591763ac5d94a8361a4b78c1b199bb05299cc004
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522129"
---
# <a name="cache-policy-hot-and-cold-cache"></a>快取原則 (冷熱快取 )

Azure 資料資源管理員將其引入的數據儲存在可靠的存儲(最常見的 Azure Blob 儲存)中,遠離其實際處理(例如 Azure 計算)節點。 為了加快對該數據的查詢,Azure 數據資源管理員在其處理節點 SSD 上甚至 RAM 中緩存此數據(或其部分)。 Azure 資料資源管理器包括一個複雜的緩存機制,旨在智慧地決定要緩存哪些數據物件。 快取使用 Azure 資料資源管理員能夠描述它使用的資料工件(如列索引和列資料分片),以便更「重要」的數據可以優先處理。

雖然緩存所有引入的數據時都實現了最佳查詢性能,但某些數據通常並不能證明在本地 SSD 存儲中保持"溫暖"的成本是合理的。
例如,許多團隊認為很少訪問較舊的日誌記錄不太重要。
他們寧願在查詢此數據時降低性能,也不願一直付費使其保持溫暖。

Azure 資料資源管理員快取提供一個粒度**快取策略**,客戶可以使用該策略來區分兩個資料緩取策略:**熱資料快取**與**冷資料快取**。 Azure 資料資源管理器快取嘗試將屬於本地 SSD(或 RAM)中的熱資料快取中的所有資料保留到熱資料緩存的已定義大小。 剩餘的本地 SSD 空間將用於保存未歸類為熱的數據。 此設計的一個有用含義是,從可靠存儲中載入大量冷數據的查詢不會將數據從熱數據緩存中逐出。 因此,對涉及熱數據緩存中數據的查詢不會有重大影響。

設定熱快取策略的主要含義包括:
* **成本**可靠存儲的成本可能大大低於本地 SSD(例如,在 Azure 中,它目前大約便宜 45 倍)。
* **效能**當數據位於本地 SSD 中時,可以更快地查詢數據。 對於範圍查詢(即掃描大量數據的查詢)尤其如此。  

[控制命令](cache-policy.md)使管理員能夠管理緩存策略。

## <a name="how-the-cache-policy-gets-applied"></a>如何套用快取原則

將資料引入 Azure 資料資源管理員時,系統會追蹤執行引入的日期/時間並創建範圍。 擴展盤的引入日期/時間值(或最大值,如果從多個預先存在的擴展盤區生成)用於評估緩存策略。

預設情況下,有效的策略是`null`,這意味著所有數據都被視為**熱**。
非`null`表級策略覆蓋資料庫級策略。

> [!Note] 
> 可以使用引入屬性`creationTime`指定引入日期/時間的值。 

## <a name="scoping-queries-to-hot-cache"></a>將查詢範圍範圍為熱快取

Kusto 支援僅將緩存數據限定為熱快取數據的查詢。 有幾個方式可做到這點：

- 將呼叫的客戶端要求`query_datascope`屬性 新增到查詢`default``all`可能`hotcache`值: 與 。
- 在查詢`set`文本中使用語句`set query_datascope='...'`: ,可能的值與用戶端請求屬性的值相同。
- 在`datascope=...`查詢正文中的表引用後立即添加文本。 可能的值是 `all` 和 `hotcache`。

該`default`值指示群集默認設置的使用,這些設置確定查詢應涵蓋所有數據。



如果不同方法之間存在差異:`set`優先於用戶端請求屬性,並且為表引用指定值優先於兩者。

例如,在以下查詢中,所有表引用將僅使用 hotcache 資料,但`T`限定到 所有資料的第二個引用除外:

```kusto
set query_datascope="hotcache";
T | union U | join (T datascope=all | where Timestamp < ago(365d) on X
```

## <a name="cache-policy-vs-retention-policy"></a>快取原則與保留原則

快取策略獨立於[保留政策](./retentionpolicy.md): 
- 緩存策略定義了如何確定資源的優先順序,以便對重要數據的查詢更快且能夠抵禦查詢對不太重要數據的影響。 
- 保留策略定義表/資料庫中可查詢數據的範圍(具體來說, `SoftDeletePeriod`)。

建議使用此策略進行配置,以便根據預期的查詢模式在成本和性能之間實現最佳平衡。

範例：
* `SoftDeletePeriod`= 56d
* `hot cache policy`= 28d

在此範例中,過去 28 天的數據將位於群集 SSD 上,**另外**28 天數據將儲存在 Azure Blob 儲存中。 您可以在整個 56 天的數據上運行查詢。 

## <a name="cache-policy-does-not-make-kusto-a-cold-storage-technology"></a>快取策略不會讓 Kusto 成為冷儲存技術

Azure 資料資源管理員旨在執行臨時查詢,中間結果集適合群集的總 RAM。 對於大型作業(如映射縮減),您希望在持久存儲(如 SSD)中存儲中間結果,請使用連續匯出功能。 這使您能夠使用 HDInsight 或 Azure 資料塊等服務執行長時間運行的批處理查詢。