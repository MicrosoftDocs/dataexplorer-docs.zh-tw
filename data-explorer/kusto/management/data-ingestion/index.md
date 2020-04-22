---
title: 資料內嵌 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的資料內嵌。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: f41304ae4ac51081cd61c41856ed5e7e08ed6f7a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490373"
---
# <a name="data-ingestion"></a>資料擷取

用來將資料新增至資料表並使資料可供查詢的程序。
視資料表是否預先存在而定，此程序需要[資料庫管理員、資料庫擷取器、資料庫使用者或資料表管理員權限](../access-control/role-based-authorization.md)。

資料內嵌程序是由數個步驟所組成：

1. 從資料來源擷取資料。
1. 剖析和驗證資料。
1. 比對資料的結構描述與目標 Kusto 資料表的結構描述，或建立目標資料表 (如果尚未存在)。
1. 組織資料行中的資料。
1. 編製資料索引。
1. 編碼和壓縮資料。
1. 將所產生的 Kusto 儲存體成品保存在儲存體中。
1. 執行所有相關的更新原則 (如果有的話)。
1. 「認可」資料內嵌，使其可供查詢。

> [!NOTE]
> 視特定案例而定，可能會略過上述某些步驟。
> 例如，透過串流擷取端點所擷取的資料會略過步驟 4、5、6 和 9。 因為資料「已清理」，所以這些步驟會在背景中完成。
> 另一個範例是，如果資料來源是對相同叢集進行 Kusto 查詢的結果，則不需要剖析和驗證資料。

> [!WARNING]
> 在 Kusto 中內嵌至資料表的資料會受限於資料表的有效**保留原則**。
> 除非明確設定於資料表上，否則有效保留原則會衍生自資料庫的保留原則。 因此，當您將資料擷取至 Kusto 時，請確定資料庫的保留原則適合您的需要。 如果不是，請在資料表層級明確覆寫。 若沒有這麼做，可能會因為資料庫的保留原則而導致系統「無訊息」地刪除您的資料。 如需詳細資料，請參閱[保留原則](https://kusto.azurewebsites.net/docs/concepts/retentionpolicy.html)。

如需資料內嵌屬性，請參閱[資料內嵌屬性](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)。
如需資料內嵌支援的格式清單，請參閱[資料格式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)。



## <a name="ingestion-methods"></a>擷取方法

有幾種方法可以內嵌資料，每種方法都有自己的特性：

1. **內嵌式內嵌 (推送)** ：系統會將控制命令 ([.ingest inline](./ingest-inline.md)) 傳送至引擎，並將資料內嵌為命令文字本身的一部分。
   這個方法主要用於臨機操作測試，不應該用於生產用途。
1. **從查詢內嵌**：系統會將控制命令 ([.set、.append、.set-or-append 或 .set-or-replace](./ingest-from-query.md)) 傳送至引擎，並將資料間接指定為查詢或命令的結果。
   這個方法可用來從未經處理資料表產生報告資料表，或建立小型暫存資料表以供進一步分析。
1. **從儲存體內嵌 (提取)** ：系統會將控制命令 ([.ingest into](./ingest-from-storage.md)) 傳送至引擎，並且讓儲存在某些外部儲存體 (例如 Azure Blob 儲存體) 中的資料可由引擎存取，並由命令指向。
   這個方法可有效率地大量內嵌資料，但會在執行內嵌的用戶端上增加一些負擔，才不會讓具有並行內嵌的叢集負擔過重 (或冒險經由資料內嵌取用所有叢集資源)，因而降低查詢的效能。
1. **已排入佇列的內嵌**：資料會上傳到外部儲存體 (例如 Azure Blob 儲存體)。 通知會傳送至佇列 (例如，Azure 佇列或事件中樞)。
   這是生產環境中所用的主要方法，因為其具有非常高的可用性，不需要用戶端本身執行容量管理，並可妥善處理大量附加。 這有時稱為「原生內嵌」。


|方法             |Latency                 |Production|大量|可用性|同步性|
|-------------------|------------------------|----------|----|------------|-------------|
|內嵌式內嵌   |秒數 + 內嵌時間   |否        |否  |Kusto 引擎|同步  |
|從查詢內嵌  |查詢時間 + 內嵌時間|是       |是 |Kusto 引擎|同步  |
|從儲存體內嵌|秒數 + 內嵌時間   |是       |是 |Kusto 引擎|兩者         |
|已排入佇列的內嵌   |分鐘                 |是       |是 |儲存體     |非同步的 |

## <a name="validation-policy-during-ingestion"></a>內嵌期間的驗證原則

在從儲存體擷取時，來源資料會在剖析過程中進行驗證。
驗證原則會指出如何因應剖析失敗。 其中包括兩個屬性：

* `ValidationOptions`:在此，`0`表示不應執行驗證，`1` 會驗證具有相同欄位數的所有記錄 (適用於 CSV 檔案和類似檔案)，而 `2` 表示要忽略並未以雙引號括住的欄位。

* `ValidationImplications`：`0` 表示驗證失敗應會使整個內嵌失敗，而 `1`表示應該忽略驗證失敗。