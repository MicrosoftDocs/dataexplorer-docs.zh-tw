---
title: 資料匯出 - Azure 資料總管
description: 本文說明 Azure 資料總管中的資料匯出。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/09/2019
ms.openlocfilehash: e6afcf86a02f1f74fe024c1b94d7f9458a72a4e7
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763426"
---
# <a name="data-export"></a>資料匯出

資料匯出是執行 Kusto 查詢及寫入其結果的程序。 查詢結果稍後可供檢查。

有數種方法可進行資料匯出：

## <a name="client-side-export"></a>用戶端匯出
  使用最簡單的形式，可以在用戶端上進行資料匯出。 用戶端會對服務執行查詢、讀回結果，然後將其寫入。
這種形式的資料匯出依賴用戶端工具來執行匯出，通常見於工具執行所在的本機檔案系統。 支援此模型的工具包括 [Kusto.Explorer](../../tools/kusto-explorer.md)和 [Web UI](../../../web-query-data.md)。

## <a name="service-side-export-pull"></a>服務端匯出 (提取)
  如果匯出的目標是與查詢位於相同或不同叢集/資料庫的資料表，請在目標資料表上使用「從查詢內嵌」。 此流程會執行查詢，並將其結果立即內嵌到資料表中。 如需詳細資訊，請參閱[從查詢內嵌](../../management/data-ingestion/ingest-from-query.md)。

## <a name="service-side-export-push"></a>服務端匯出 (推送)
  上述方法 ([用戶端匯出](#client-side-export)及[服務端匯出 (提取)](#service-side-export-pull)) 都會受到限制。 查詢結果必須在執行查詢的生產者和寫入其結果的取用者之間，透過單一網路連線進行串流處理。
對於可調整的資料匯出，請使用「推送」匯出模型，其中執行查詢的服務也會以最佳化方式寫入其結果。 此模型會透過一組 `.export` 控制命令公開，而這些命令支援將查詢結果匯出至[外部資料表](export-data-to-an-external-table.md)、[SQL 資料表](export-data-to-sql.md)或[外部 Blob 儲存體](export-data-to-storage.md)。
  
  服務端匯出命令受限於叢集的可用資料匯出容量。
您可以執行 [show capacity 命令](../../management/diagnostics.md#show-capacity)來檢視叢集的總計、耗用和剩餘的資料匯出容量。

## <a name="recommendations-for-secret-management-when-using-data-export-commands"></a>使用資料匯出命令時的秘密管理建議

理想的情況是將資料匯出至遠端目標，例如 Azure Blob 儲存體和 Azure SQL Database。 隱含地使用安全性主體的認證，其可執行資料匯出命令。 這種方法在某些情況下並不可行。 例如，Azure Blob 儲存體不支援安全性主體的概念，僅支援其自身的權杖。
此功能支援引進必要的認證並內嵌為資料匯出控制命令的一部分。

若要以安全的方式執行匯出：

* 傳送秘密時，請使用[模糊字串常值](../../query/scalar-data-types/string.md#obfuscated-string-literals) (例如 `h@"..."`)。 密碼會遭到清除，使其不會出現在內部發出的任何追蹤中。

* 安全地儲存密碼和類似的秘密，並視需要使用應用程式加以「提取」。
