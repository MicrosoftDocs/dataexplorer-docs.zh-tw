---
title: 用戶端函式庫概述 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的用戶端庫概述。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 2eb567062402f3c00fce6b12bf81feb507ca6c23
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610167"
---
# <a name="client-libraries-overview"></a>用戶端函式庫概述

下表列出了為查詢、引入和 ARM/RP 管理提供的不同庫。 使用這些庫是使用 Azure API 和以程式設計方式呼叫 Azure 資料資源管理器功能的推薦方法。 


|    語言_功能        |    查詢        |    擷取        |    ARM/RP 管理        |
|------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------    |------------------------------------------------------------------------------------------------------------------------------    |
|    .Net        |    [努埃伊](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data/)            |    [努埃伊](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest/)        |    [努埃伊](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/1.0.0)         |
|    .淨標準        |    [努埃伊](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/)        |    [努埃伊](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard/)        |            |
|    Java        |    [馬文](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)[·吉特胡布](https://github.com/Azure/azure-kusto-java/tree/master/data)        |    [馬文](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)[·吉特胡布](https://github.com/Azure/azure-kusto-java/tree/master/ingest)        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto.v2019_01_21/azure-mgmt-kusto)        |
|    Javascript        |             |             |    [Npm](https://www.npmjs.com/package/@azure/arm-kusto)         |
|    NodeJS        |    [npm](https://www.npmjs.com/package/azure-kusto-data) [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data)        |    [npm](https://www.npmjs.com/package/azure-kusto-ingest)       [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest)        |    [Npm](https://www.npmjs.com/package/azure-arm-kusto/v/2.0.0)        |
|    Python        |    [皮皮](https://pypi.org/project/azure-kusto-ingest/)    [吉胡布](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)        |    [皮皮](https://pypi.org/project/azure-kusto-data/)      [吉胡布](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest)        |    [皮皮](https://pypi.org/project/azure-mgmt-kusto/0.3.0/)        |
|    R        |    [CRAN](https://cran.r-project.org/web/packages/AzureKusto/index.html)               |             |            |
|    Go        |    [GitHub](https://github.com/Azure/azure-kusto-go)        |    [GitHub](https://github.com/Azure/azure-kusto-go/tree/master/kusto/ingest)        |        [GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/kusto/mgmt/2019-01-21/kusto)        |
|    Ruby        |             |             |    [寶石](https://rubygems.org/gems/azure_mgmt_kusto/versions/0.17.1)         |
|    Powershell        |    [努埃伊](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [努埃伊](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [套件](https://www.powershellgallery.com/packages/Az.Kusto/)         |
|    Azure CLI        |             |             |    [Azure Cli](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)         |
|    REST API        |    [REST](rest/index.md)        |    [REST](rest/index.md)        |     [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/azure-kusto/resource-manager/Microsoft.Kusto)         |
|    TypeScript        |             |             |        [Npm](https://www.npmjs.com/package/@azure/arm-kusto/v/2.0.0)        |
|      |      |      |      |


## <a name="tools-and-integrations"></a>工具與整合

* 最輕的:[裸體](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/) 
* 一鍵引入套件[:GitHub](https://github.com/Azure/azure-kusto-ingestion-tools) 
* 卡夫卡:[吉特胡布](https://github.com/Azure/kafka-sink-azure-kustoLogstash)
* 紀錄: [GitHub](https://github.com/Azure/logstash-output-kusto) 
* 火花:[馬文](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)