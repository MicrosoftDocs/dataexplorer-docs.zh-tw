---
title: 包含檔案
description: 包含檔案
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 07/13/2020
ms.author: orspodek
ms.reviewer: alexefro
ms.custom: include file
ms.openlocfilehash: 1ee658d2c16bcf4174bb82d61ddc8c9b2d7d126f
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423232"
---
## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>使用串流內嵌將資料內嵌至您的叢集

支援兩種串流內嵌類型：

* [**事件中樞**](../ingest-data-event-hub.md)或[**IoT 中樞**](../ingest-data-iot-hub.md)，用來做為資料來源。
* **自訂**內嵌需要您撰寫使用其中一個 Azure 資料總管[用戶端程式庫](../kusto/api/client-libraries.md)的應用程式。 如需範例應用程式，請參閱[串流內嵌範例](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample)。

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>選擇適當的串流內嵌類型

|條件|事件中樞|自訂內嵌|
|---------|---------|---------|
|內嵌初始化與可供查詢的資料之間的資料延遲 | 較長延遲 | 延遲較短  |
|開發額外負荷 | 快速且輕鬆的設定，無需任何開發負擔 | 應用程式的高開發額外負荷，可處理錯誤並確保資料一致性 |
