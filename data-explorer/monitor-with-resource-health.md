---
title: 使用資源健康狀態監視 Azure 資料總管
description: 使用 Azure 資源健康狀態來監視 Azure 資料總管資源。
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 297779b6431c15436e175a3269b2291340c51b79
ms.sourcegitcommit: b8415e01464ca2ac9cd9939dc47e4c97b86bd07a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88028522"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>使用資源健康狀態 (Preview 來監視 Azure 資料總管) 

適用于 Azure 資料總管的[資源健康狀態](/azure/service-health/resource-health-overview)會通知您 azure 資料總管資源的健康狀態，並提供可採取動作的建議來疑難排解問題。 資源健康狀態會每隔1-2 分鐘更新一次，並報告資源的目前和過去健全狀況。 

資源健康狀態藉由檢查各種健全狀況狀態檢查（例如，）來判斷 Azure 資料總管資源的健全狀況：
* 資源可用性
* 查詢失敗

## <a name="access-resource-health-reporting"></a>存取資源健康狀態報告

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，從 [服務清單] 選取 [**監視**]。
1. 選取 [**服務健康狀態**  >  **資源健康狀態**]。
1. 在 [**訂**用帳戶] 下拉式清單中，選取您的訂用帳戶。 在 [**資源類型**] 下拉式清單中，選取 [ **Azure 資料總管**]。
1. 產生的資料表會列出所選訂用帳戶中的所有資源。 每個資源都會有一個健全狀況狀態圖示，指出資源健康狀態。
1. 選取您的資源，以查看其[資源健康狀態](#resource-health-status)和建議。

    ![概觀](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>資源健康情況狀態

資源的健康狀態會顯示為下列其中一個狀態： [可用]、[無法使用] 和 [未知]。

### <a name="available"></a>可用

[**可用**] 的健全狀況狀態表示您的 Azure 資料總管資源狀況良好，且沒有任何問題。

![可用](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>[無法使用]

健全狀況狀態為 [**無法使用**] 表示您的 Azure 資料總管資源發生問題，導致查詢和內嵌無法使用。 例如，Azure 資料總管資源中的節點可能會非預期地重新開機。 如果您的 Azure 資料總管資源持續處於此狀態長時間，請聯絡[支援]()人員。

![[無法使用]](media/monitor-with-resource-health/unavailable.png)

> [!TIP]
> 您可以使用 [[系統資訊] 命令](kusto/management/systeminfo.md)來尋找問題的來源。

### <a name="unknown"></a>Unknown

健全狀況狀態為 [**未知**] 表示**資源健康狀態**未收到此 Azure 資料總管資源的相關資訊超過10分鐘。 此狀態不是 Azure 資料總管資源健康情況的最終指示，而是疑難排解程式中的重要資料點。 如果您的 Azure 資料總管叢集如預期般運作，則狀態會在幾分鐘內變更為 [**可用**]。 **未知**的健全狀況狀態可能會建議平臺中的事件會影響資源。 

> [!TIP]
> 如果 Azure 資料總管叢集資源健康狀態為 [已停止]，則其為**不明**。

![Unknown](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>歷程記錄資訊

在**資源健康狀態**] 窗格中 > 健康情況歷程**記錄**，最多可存取四周的資源健康狀態資訊。 選取箭號，以取得此窗格中所報告之健康情況事件問題的詳細資訊。 

![記錄](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>後續步驟

* [設定資源健康狀態警示](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [教學課程：在 Azure 中內嵌和查詢監視資料資料總管](ingest-data-no-code.md)
* [使用計量監視 Azure 資料總管效能、健康情況和使用量](using-metrics.md)
