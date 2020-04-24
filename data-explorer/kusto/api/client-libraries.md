---
title: 用戶端程式庫總覽-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的用戶端程式庫總覽。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 2a8e66e726bd4dbd49cf402178117d9d01c65d34
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108179"
---
# <a name="client-libraries-overview"></a>用戶端程式庫總覽

下表列出針對查詢、內嵌和 ARM/RP 管理所提供的不同程式庫。 建議使用這些程式庫來利用 Azure Api，並以程式設計方式叫用 Azure 資料總管功能。 


|    Language\Functionality        |    查詢        |    擷取        |    ARM/RP 管理        |
|------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------    |------------------------------------------------------------------------------------------------------------------------------    |
|    .Net        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data/)            |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest/)        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/1.0.0)         |
|    .Net Standard        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/)        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard/)        |            |
|    Java        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data) [GitHub](https://github.com/Azure/azure-kusto-java/tree/master/data)        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest) [GitHub](https://github.com/Azure/azure-kusto-java/tree/master/ingest)        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto.v2019_01_21/azure-mgmt-kusto)        |
|    Javascript        |             |             |    [npm](https://www.npmjs.com/package/@azure/arm-kusto)         |
|    NodeJS        |    [npm](https://www.npmjs.com/package/azure-kusto-data) [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data)        |    [npm](https://www.npmjs.com/package/azure-kusto-ingest)       [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest)        |    [npm](https://www.npmjs.com/package/azure-arm-kusto/v/2.0.0)        |
|    Python        |    [Pypi](https://pypi.org/project/azure-kusto-ingest/)    [GitHub](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)        |    [Pypi](https://pypi.org/project/azure-kusto-data/)      [GitHub](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest)        |    [Pypi](https://pypi.org/project/azure-mgmt-kusto/0.3.0/)        |
|    R        |    [CRAN](https://cran.r-project.org/web/packages/AzureKusto/index.html)               |             |            |
|    Go        |    [GitHub](https://github.com/Azure/azure-kusto-go)        |    [GitHub](https://github.com/Azure/azure-kusto-go/tree/master/kusto/ingest)        |        [GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/kusto/mgmt/2019-01-21/kusto)        |
|    Ruby        |             |             |    [寶藏](https://rubygems.org/gems/azure_mgmt_kusto/versions/0.17.1)         |
|    Powershell        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [套件](https://www.powershellgallery.com/packages/Az.Kusto/)         |
|    Azure CLI        |             |             |    [Azure Cli](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)         |
|    REST API        |    [REST](rest/index.md)        |    [REST](rest/index.md)        |     [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/azure-kusto/resource-manager/Microsoft.Kusto)         |
|    TypeScript        |             |             |        [Npm](https://www.npmjs.com/package/@azure/arm-kusto/v/2.0.0)        |
|      |      |      |      |


## <a name="tools-and-integrations"></a>工具與整合

* LightIngest： [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/) 
* 單鍵內嵌套件： [GitHub](https://github.com/Azure/azure-kusto-ingestion-tools) 
* Kafka： [GitHub](https://github.com/Azure/kafka-sink-azure-kusto)
* Logstash： [GitHub](https://github.com/Azure/logstash-output-kusto) 
* Spark： [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)