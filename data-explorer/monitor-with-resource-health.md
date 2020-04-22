---
title: 使用資源執行狀況監視 Azure 資料資源管理員
description: 使用 Azure 資源運行狀況監視 Azure 資料資源管理器資源。
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 9b8b1b40e972d7719ef2cf495ed12cb4ed3478fd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81495597"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>使用資源執行狀況監視 Azure 資料資源管理員(預覽)

Azure 資料資源管理員[的資源運行狀況](/azure/service-health/resource-health-overview)會通知您 Azure 資料資源管理器資源的運行狀況,並提供可操作的建議來解決問題。 資源運行狀況每 1-2 分鐘更新一次,並報告資源的當前運行狀況和過去運行狀況。 

資源執行狀況透過檢查各種執行狀況檢查(如:
* 資源可用性
* 查詢失敗

## <a name="access-resource-health-reporting"></a>存取資源執行狀況報告

1. 在[Azure 門戶](https://portal.azure.com/)中,從服務清單中選擇 **「監視器**」。
1. 選擇**服務執行狀況** > **資源執行狀況**。
1. 在 **「訂閱」** 下拉清單中,選擇您的訂閱。 在 **「資源類型**」 下拉下拉下,選擇**Azure 資料資源管理員**。
1. 生成的表列出所選訂閱中的所有資源。 每個資源都將有一個運行狀況狀態圖示,指示資源運行狀況。
1. 選擇您的資源以檢視資源[執行狀況與建議](#resource-health-status)。

    ![概觀](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>資源健康情況狀態

將顯示資源的運行狀況,並顯示以下狀態之一,可用、不可用和未知。

### <a name="available"></a>可用

"**可用'** 的執行狀況表示 Azure 資料資源管理員資源狀況良好且沒有任何問題。

![可用](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>無法使用

運行狀況為 **「不可用**」表示 Azure 資料資源管理員資源存在持續問題,導致它無法用於查詢和引入。 例如,Azure 資料資源管理器資源中的節點可能意外重新啟動。 如果 Azure 資料資源管理員資源長時間處於此狀態,請與[支援]()人員聯絡 。

![無法使用](media/monitor-with-resource-health/unavailable.png)

### <a name="unknown"></a>Unknown

運行狀況為 **「未知**」表示**資源運行狀況**已超過 10 分鐘未收到有關此 Azure 資料資源管理員資源的資訊。 此狀態不是 Azure 數據資源管理器資源運行狀況的明確指示,而是故障排除過程中的重要數據點。 如果 Azure 資料資源管理器群集按預期運行,則狀態將在幾分鐘內更改為 **"可用**"。 **未知**運行狀況可能表示平臺中的事件正在影響資源。 

> [!TIP]
> Azure 資料資源管理器群集資源運行狀況如果處於已停止狀態,則為 **"未知**"。

![Unknown](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>歷程記錄資訊

在 **「資源運行狀況**>**運行狀況歷史記錄**」中,訪問長達四周的資源運行狀況狀態資訊。 選擇箭頭,獲取有關此窗格中報告的運行狀況事件問題的其他資訊。 

![記錄](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>後續步驟

* [設定資源執行狀況警示](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [教學:在 Azure 資料資源管理員中引入與查詢監視資料](ingest-data-no-code.md)
* [使用指標監控 Azure 資料資源管理員的效能、執行狀況和使用](using-metrics.md)