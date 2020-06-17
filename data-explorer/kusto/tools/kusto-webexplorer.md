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
ms.openlocfilehash: d53f12c4a0c4dd2bce669dbe004b8f325db27af5
ms.sourcegitcommit: 4986354cc1ba25c584e4f3c7eac7b5ff499f0cf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/16/2020
ms.locfileid: "84856397"
---
# <a name="kustowebexplorer"></a>Kusto. WebExplorer

Kusto. WebExplorer 是一個 web 應用程式，可以用來傳送查詢和控制命令給 Kusto 服務。 應用程式會裝載于 https://dataexplorer.azure.com/ ，並由進行簡短連結 https://aka.ms/kwe 。



Kusto，WebExplorer 也可以由 HTML IFRAME 中的其他 web 入口網站主控。
（例如，這是由[Azure 入口網站](https://portal.azure.com)完成）。如需如何裝載它的詳細資訊，以及它所使用的摩納哥編輯器，請參閱[摩納哥 IDE](../api/monaco/monaco-kusto.md) 。

## <a name="connect-to-multiple-clusters"></a>連接到多個叢集

您現在可以連接多個叢集，並在資料庫和叢集之間切換。
此工具的設計目的是要輕鬆地識別您所連接的叢集和資料庫。

![替代文字](./Images/KustoTools-WebExplorer/AddingCluster.gif "AddingCluster")

## <a name="recall-results"></a>重新叫用結果

在分析期間，我們通常會執行多個查詢，而且可能需要重新流覽先前查詢的結果。 您可以使用這項功能來召回結果，而不需要重新執行查詢。 從本機用戶端快取提供資料。

![替代文字](./Images/KustoTools-WebExplorer/RecallResults.gif "RecallResults")

## <a name="enhanced-results-grid-control"></a>增強的結果格線控制項

資料表方格可讓您選取多個資料列、資料行和資料格。 藉由選取多個儲存格（例如 Excel）並樞紐分析表來計算匯總。

![替代文字](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "EnhancedGrid")

## <a name="intellisense--formatting"></a>Intellisense & 格式

您可以使用「Shift + Alt + F」快速鍵、程式碼折迭（大綱）和 IntelliSense，來使用美觀的列印格式。

![替代文字](./Images/KustoTools-WebExplorer/Formating.gif "格式設定")

## <a name="deep-linking"></a>深層連結

您可以只複製深層連結或深層連結和查詢。 您也可以使用下列範本，將 URL 格式化以包含叢集、資料庫和查詢：

`https://dataexplorer.azure.com/`[ `clusters/` *Cluster* [ `/databases/` *Database* [ `?` *Options*]]]

您可以指定下列選項：

* `workspace=empty`：表示要建立新的空白工作區（不會重新叫用先前的叢集、索引標籤和查詢）。



![替代文字](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="feedback"></a>意見反應

您可以透過工具提交意見反應。
![替代文字](./Images/KustoTools-WebExplorer/Feedback.gif "意見反應")
