---
title: 使用資源健康狀態監視 Azure 資料總管
description: 使用 Azure 資源健康狀態來監視 Azure 資料總管資源。
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/31/2020
ms.openlocfilehash: 630b03fc0e89005f1031aba4cbd1a86d803ff275
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942024"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>使用資源健康狀態 (Preview 來監視 Azure 資料總管) 

適用于 Azure 資料總管的[資源健康狀態](/azure/service-health/resource-health-overview)會通知您 Azure 資料總管資源的健康情況，並提供可採取動作的建議以疑難排解問題。 資源健康狀態會每隔1-2 分鐘更新一次，並報告您的資源目前和過去的健康情況。 

資料總管資源健康狀態藉由檢查各種健全狀況狀態檢查（例如：
* 資源可用性
* 查詢失敗

## <a name="access-resource-health-reporting"></a>存取資源健康狀態報告

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，從服務清單中選取 [ **監視** ]。
1. 選取 [**服務健康**情況  >  **資源健康狀態**]。
1. 在 **訂** 用帳戶下拉式清單中，選取您的訂用帳戶。 在 [ **資源類型** ] 下拉式清單中，選取 [ **Azure 資料總管**]。
1. 產生的資料表會列出所選訂用帳戶中的所有資源。 每個資源都會有一個指出資源健康狀態的健全狀況狀態圖示。
1. 選取您的資源以查看其 [資源健康情況狀態](#resource-health-status) 和建議。

    ![概觀](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>資源健康情況狀態

資源的健康狀態會顯示下列其中一個狀態： [可用]、[無法使用] 和 [不明]。

### <a name="available"></a>可用

健康狀態為 [ **可用** ] 表示您的 Azure 資料總管資源狀況良好且沒有任何問題。

:::image type="content" source="media/monitor-with-resource-health/available.png" alt-text="Azure 資料總管資源的資源健康狀態頁面螢幕擷取畫面。狀態會列出為 [可用]，並會反白顯示。" border="false":::

### <a name="unavailable"></a>[無法使用]

健全狀況狀態為 [ **無法使用** ] 表示 Azure 資料總管資源發生問題，導致無法在查詢和內嵌時使用。 例如，Azure 資料總管資源中的節點可能未預期地重新開機。 如果您的 Azure 資料總管資源在一段長時間內仍維持此狀態，請聯絡 [支援]()人員。

:::image type="content" source="media/monitor-with-resource-health/unavailable.png" alt-text="Azure 資料總管資源的資源健康狀態頁面螢幕擷取畫面。狀態會列出為 [可用]，並會反白顯示。" border="false":::

> [!TIP]
> 您可以使用 [ [系統資訊] 命令](kusto/management/systeminfo.md) 找出問題的來源。

### <a name="unknown"></a>Unknown

健康狀態為 [ **未知** ] 表示 **資源健康狀態** 未收到此 Azure 資料總管資源的相關資訊超過10分鐘。 此狀態不是 Azure 資料總管資源健康狀態的明確指示，但在疑難排解過程中是重要的資料點。 如果您的 Azure 資料總管叢集如預期般運作，則在幾分鐘內狀態將會變更為 [ **可用** ]。 **未知**的健康狀態可能會建議平臺中的事件影響資源。 

> [!TIP]
> 如果 Azure 資料總管叢集資源健康狀態為「已停止」狀態，則會是 **未知** 的。

:::image type="content" source="media/monitor-with-resource-health/unknown.png" alt-text="Azure 資料總管資源的資源健康狀態頁面螢幕擷取畫面。狀態會列出為 [可用]，並會反白顯示。" border="false":::

## <a name="historical-information"></a>歷程記錄資訊

在 **資源健康狀態** 窗格中 > 健康情況歷程 **記錄**，最多可存取四周的資源健康狀態資訊。 針對此窗格中所報告的健康情況事件問題，選取箭號以取得詳細資訊。 

![歷程記錄](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>後續步驟

* [設定資源健康狀態警示](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [教學課程：在 Azure 資料總管中內嵌和查詢監視資料](ingest-data-no-code.md)
* [使用計量來監視 Azure 資料總管效能、健康情況和使用量](using-metrics.md)
