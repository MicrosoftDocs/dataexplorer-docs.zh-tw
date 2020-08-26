---
title: 使用 Azure 入口網站在 Azure 資料總管叢集上設定串流內嵌
description: 瞭解如何設定 Azure 資料總管叢集，並使用 Azure 入口網站以串流內嵌開始載入資料。
author: orspod
ms.author: orspodek
ms.reviewer: alexefro
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/13/2020
ms.openlocfilehash: 417d2b45e53abeba40f099a33880912a9300bc31
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874846"
---
# <a name="configure-streaming-ingestion-on-your-azure-data-explorer-cluster-using-the-azure-portal"></a>使用 Azure 入口網站在 Azure 資料總管叢集上設定串流內嵌

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-streaming.md)
> * [C#](ingest-data-streaming-csharp.md)

[!INCLUDE [ingest-data-streaming-intro](includes/ingest-data-streaming-intro.md)]

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 建立 [Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>在您的叢集上啟用串流內嵌

### <a name="enable-streaming-ingestion-while-creating-a-new-cluster-in-the-azure-portal"></a>在 Azure 入口網站中建立新叢集時啟用串流內嵌

您可以在建立新的 Azure 資料總管叢集時啟用串流內嵌。 

**在 [設定**] 索引標籤中，選取 [**串流**內嵌  >  **于**]。

:::image type="content" source="media/ingest-data-streaming/cluster-creation-enable-streaming.png" alt-text="在 Azure 資料總管中建立叢集時啟用串流內嵌":::

### <a name="enable-streaming-ingestion-on-an-existing-cluster-in-the-azure-portal"></a>在 Azure 入口網站中的現有叢集上啟用串流內嵌

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 
1. 在 [設定] 中 **，選取 [****設定**]。 
1. **在 [設定**] 窗格中，選取 [**開啟**] 以啟用**串流**內嵌。
1. 選取 \[儲存\]。

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-on.png" alt-text="在 Azure 資料總管中開啟串流內嵌":::

> [!WARNING]
> 在啟用串流內嵌之前，請先檢查 [限制](#limitations) 。

## <a name="create-a-target-table-and-define-the-policy-in-the-azure-portal"></a>在 Azure 入口網站中建立目標資料表並定義原則

1. 在 Azure 入口網站中，流覽至您的叢集。
1. 選取 [查詢]  。

    :::image type="content" source="media/ingest-data-streaming/cluster-select-query-tab.png" alt-text="在 Azure 資料總管入口網站中選取查詢以啟用串流內嵌":::

1. 若要建立會透過串流內嵌接收資料的資料表，請將下列命令複製到 [ **查詢] 窗格** ，然後選取 [ **執行**]。

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    :::image type="content" source="media/ingest-data-streaming/create-table.png" alt-text="建立將內嵌串流至 Azure 資料總管的資料表":::

1. 針對您所建立的資料表或包含此資料表的資料庫，定義 [串流內嵌原則](kusto/management/streamingingestionpolicy.md) 。 
 
    > [!TIP]
    > 在資料庫層級定義的原則會套用至資料庫中的所有現有和未來資料表。 
    
1. 將下列其中一個命令複製到 [ **查詢] 窗格** 中，然後選取 [ **執行**]。

    ```kusto
    .alter table TestTable policy streamingingestion enable
    ```

    或

    ```kusto
    .alter database StreamingTestDb policy streamingingestion enable
    ```

    :::image type="content" source="media/ingest-data-streaming/define-streaming-ingestion-policy.png" alt-text="在 Azure 資料總管中定義串流內嵌原則":::

[!INCLUDE [ingest-data-streaming-use](includes/ingest-data-streaming-types.md)]

[!INCLUDE [ingest-data-streaming-disable](includes/ingest-data-streaming-disable.md)]

## <a name="drop-the-streaming-ingestion-policy-in-the-azure-portal"></a>捨棄 Azure 入口網站中的串流內嵌原則

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集，然後選取 [ **查詢**]。 
1. 若要從資料表中卸載串流內嵌原則，請將下列命令複製到 [ **查詢] 窗格** ，然後選取 [ **執行**]。

    ```Kusto
    .delete table TestTable policy streamingingestion 
    ```

    :::image type="content" source="media/ingest-data-streaming/delete-streaming-ingestion-policy.png" alt-text="在 Azure 資料總管中刪除串流內嵌原則":::

1. 在 [設定] 中 **，選取 [****設定**]。
1. **在 [設定**] 窗格中，選取 [**關閉**] 以停用**串流**內嵌。
1. 選取 \[儲存\]。

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-off.png" alt-text="關閉 Azure 資料總管中的串流內嵌":::

[!INCLUDE [ingest-data-streaming-limitations](includes/ingest-data-streaming-limitations.md)]

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管中查詢資料](web-query-data.md)
