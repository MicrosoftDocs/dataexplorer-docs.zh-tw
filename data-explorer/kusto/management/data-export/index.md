---
title: 資料匯出 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的資料匯出。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/09/2019
ms.openlocfilehash: e779571251baa6e87953e546d71adb98e7cfde61
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490390"
---
# <a name="data-export"></a>資料匯出

資料匯出是執行 Kusto 查詢並寫入其結果的過程，讓查詢結果可供稍後檢查。

有數種方法可進行資料匯出：

* **用戶端匯出**：在最簡單的形式中，可以在用戶端上進行資料匯出 (用戶端會對服務執行查詢、讀回結果，然後將其寫入某處)。 這種形式的資料匯出依賴用戶端工具來執行匯出，最常見於工具執行所在的本機檔案系統。 支援此模型的工具包括 [Kusto.Explorer](../../tools/kusto-explorer.md)、[Web UI](https://docs.microsoft.com/azure/data-explorer/web-query-data) 


 等等。

* **服務端匯出 (提取)** ：如果匯出的目標是 Kusto 資料表 (位於與查詢相同的叢集/資料庫或其他叢集/資料庫上)，請在目標資料表上使用「從查詢內嵌」流程。 此流程會執行查詢，並將其結果立即內嵌到 Kusto 資料表中。 請參閱[資料內嵌](../data-ingestion/index.md)。



* **服務端匯出 (推送)** ：上述方法有些限制，因為查詢結果必須在進行查詢的生產者與撰寫其結果的取用者之間，透過單一網路連線進行串流處理。 針對可調整的資料匯出，Kusto 會提供「推送」匯出模型，其中執行查詢的服務也會以最佳化方式寫入其結果。 此模型會透過一組 `.export` 控制命令公開，並支援將查詢結果匯出至[外部資料表](export-data-to-an-external-table.md)、[SQL 資料表](export-data-to-sql.md)或[外部 Blob 儲存體](export-data-to-storage.md)。
  
  服務端匯出命令受限於叢集的可用資料匯出容量。 
  您可以執行 [show capacity 命令](../../management/diagnostics.md#show-capacity)來檢視叢集的總計、耗用和剩餘的資料匯出容量。

## <a name="recommendations-for-secret-management-when-using-data-export-commands"></a>使用資料匯出命令時的秘密管理建議

在理想的情況下，隱含使用執行資料匯出命令之安全性主體的認證，可將資料匯出至遠端目標 (例如 Azure Blob 儲存體和 Azure SQL Database)。 這在某些情況下並不可行 (例如，Azure Blob 儲存體不支援安全性主體的概念，僅支援其自己的權杖)。因此，Kusto 支援引進必要的認證並內嵌為資料匯出控制命令的一部分。 以下是一些建議，以確保以安全的方式完成這項作業：

將秘密傳送至 Kusto 時，請使用[模糊字串常值](../../query/scalar-data-types/string.md#obfuscated-string-literals)(例如 `h@"..."`)。
Kusto 會接著清除這些秘密，使其不會出現在內部發出的任何追蹤中。

此外，密碼和類似的秘密應該安全地儲存，並由應用程式視需要加以「提取」。
