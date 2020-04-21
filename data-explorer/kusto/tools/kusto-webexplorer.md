---
title: 庫斯托.Web資源管理員 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 Kusto.Web 資源管理器。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 1f6926df09a207cfea2b9201ef57f36932a63f74
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523863"
---
# <a name="kustowebexplorer"></a>庫斯托.Web資源管理員

Kusto.WebExplorer 是一個 Web 應用程式,可用於向 Kusto 服務發送查詢和控制命令。 應用程式託管在 []https://dataexplorer.azure.com/和短連結由https://aka.ms/kwe[ ] 。



Kusto.WebExplorer 也可以由 HTML IFRAME 中的其他 Web 入口託管。
(例如,這是由[Azure 門戶](https://portal.azure.com)完成的。有關如何主辦它的詳細資訊和它使用的摩納哥編輯器,請參閱[摩納哥 IDE。](../api/monaco/monaco-kusto.md)

## <a name="connect-to-multiple-clusters"></a>連接到多個群組

現在,您可以連接多個群集並在資料庫和群集之間切換。
該工具旨在輕鬆識別您連接到的群集和資料庫。

![alt 文字](./Images/KustoTools-WebExplorer/AddingCluster.gif "新增叢集")

## <a name="recall-results"></a>召回結果

通常,在分析期間,我們運行多個查詢,並且可能必須重新訪問以前的查詢的結果。 您可以使用此功能來調用結果,而無需重新運行查詢。 數據從本地用戶端緩存提供。

![alt 文字](./Images/KustoTools-WebExplorer/RecallResults.gif "召回結果")

## <a name="enhanced-results-grid-control"></a>增強的結果格線控制

表格線允許您選擇多個列、列和儲存格。 通過選擇多個單元格(如 Excel)來計算聚合,並透視數據。

![alt 文字](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "增強格線")

## <a name="intellisense--formatting"></a>感知&格式

您可以使用「Shift + Alt + F」快速鍵、代碼摺疊(大綱)和 IntelliSense 使用漂亮的列印格式。

![alt 文字](./Images/KustoTools-WebExplorer/Formating.gif "格式")

## <a name="deep-linking"></a>深度連結

您可以只複製深層連結或深層連結和查詢。 您還可以使用以下樣本格式化網址 以包括叢集、資料庫和查詢:

`https://dataexplorer.azure.com/`[`clusters/`*Cluster*叢`/databases/`集`?`]*資料庫*[*選項*]

可以指定以下選項:

* `workspace=empty`指示創建新的空工作區(不會呼叫以前的群集、選項卡和查詢)。



![alt 文字](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="feedback"></a>意見反應

您可以通過該工具提交反饋。
![alt 文字](./Images/KustoTools-WebExplorer/Feedback.gif "意見反應")