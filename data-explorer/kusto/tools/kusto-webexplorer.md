---
title: Kusto. WebExplorer-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 Kusto WebExplorer。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 70b25a1dbfcc3d5cba134875a63c2ac24a018277
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942279"
---
# <a name="kustowebexplorer"></a>Kusto. WebExplorer

Kusto. WebExplorer 是一種 web 應用程式，可用來將查詢和控制命令傳送至 Kusto 服務。 應用程式是由所裝載 https://dataexplorer.azure.com/ ，並由進行簡短連結 https://aka.ms/kwe 。



Kusto WebExplorer 也可以由 HTML IFRAME 中的其他入口網站所裝載。
 (例如，這是由 [Azure 入口網站](https://portal.azure.com)完成。 ) 查看 [摩納哥 IDE](../api/monaco/monaco-kusto.md) ，以取得如何裝載它的詳細資訊，以及它所使用的摩納哥編輯器。

## <a name="connect-to-multiple-clusters"></a>連接到多個叢集

您現在可以連接多個叢集，並在資料庫和叢集之間切換。
此工具的設計目的是要輕鬆地識別您所連接的叢集和資料庫。

![動畫 GIF。當您在 Azure 資料總管中按一下 [新增叢集]，並在對話方塊中輸入叢集名稱時，該叢集就會出現在左窗格中。](./Images/KustoTools-WebExplorer/AddingCluster.gif "AddingCluster")

## <a name="recall-results"></a>召回結果

通常在分析期間，我們會執行多個查詢，而且可能必須重新流覽先前查詢的結果。 您可以使用這項功能來重新叫用您的結果，而不需要重新執行查詢。 從本機用戶端快取提供資料。

![動畫 GIF。在兩個 Azure 資料總管查詢執行之後，滑鼠會移至第一個查詢，然後按一下 [重新叫用]。初始結果會再次出現。](./Images/KustoTools-WebExplorer/RecallResults.gif "RecallResults")

## <a name="enhanced-results-grid-control"></a>增強的結果方格控制項

資料表方格可讓您選取多個資料列、資料行和資料格。 選取多個資料格 (例如 Excel) 和樞紐分析表來計算匯總。

![動畫 GIF。在 Azure 中開啟 Pivot 模式之後資料總管且資料行會拖曳至樞紐分析表目的地區域之後，摘要的資料就會出現。](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "EnhancedGrid")

## <a name="intellisense--formatting"></a>Intellisense & 格式

您可以使用「Shift + Alt + F」快速鍵、程式碼折迭 (大綱) 和 IntelliSense，來使用美觀的列印格式。

![顯示 Azure 資料總管查詢的動畫 GIF。展開查詢之後，它會以粉紅色的資料行名稱變更格式（出現在同一行）。](./Images/KustoTools-WebExplorer/Formating.gif "格式設定")

## <a name="deep-linking"></a>深層連結

您只可以複製深層連結或深層連結和查詢。 您也可以使用下列範本，將 URL 格式化以包含叢集、資料庫和查詢：

`https://dataexplorer.azure.com/` [ `clusters/` *Cluster* [ `/databases/` *Database* [ `?` *Options*]]]

您可以指定下列選項：

* `workspace=empty`：指出要建立新的空白工作區 (不會重新叫用先前的叢集、索引標籤，而且查詢將會) 完成。



![動畫 GIF。[Azure 資料總管共用] 功能表隨即開啟。[剪貼簿] 專案的查詢連結會變成可見，就像是 [剪貼簿] 專案的文字和連結一樣。](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="how-to-provide-feedback"></a>如何提供意見反應

您可以透過工具提交意見反應。
![顯示 Azure 資料總管的動畫 GIF。按一下 [意見反應] 圖示時，就會開啟 [傳送我們意見反應] 對話方塊。](./Images/KustoTools-WebExplorer/Feedback.gif "意見反應")
