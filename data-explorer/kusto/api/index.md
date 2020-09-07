---
title: Azure 資料總管 API 概觀 - Azure 資料總管
description: 本文說明 API 資料總管中的實體。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: vladikb
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2020
ms.openlocfilehash: b9fd03bfd08a31d872ca3c0ef48bd96514e9eb18
ms.sourcegitcommit: 9e0289945270db517e173aa10024e0027b173b52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428391"
---
# <a name="azure-data-explorer-api-overview"></a>Azure 資料總管 API 概觀

Azure 資料總管服務支援下列通訊端點：

1. [REST API](#rest-api) 端點，您可以透過該端點查詢和管理 Azure 資料總管中的資料。
   此端點支援查詢和[控制命令](../management/index.md)的 [Kusto 查詢語言](../query/index.md)。
1. [MS-TDS](#ms-tds) 端點，其會實作由 Microsoft SQL Server 產品所使用的 Microsoft 表格式資料流 (TDS) 通訊協定子集。
   這個端點適用於知道如何與 SQL Server 端點通訊以進行查詢的工具。
1. [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftkusto) 端點是 Azure 服務的標準方法。 端點可用來管理資源，例如 Azure 資料總管叢集。

## <a name="rest-api"></a>REST API

與任何 Azure 資料總管服務通訊的主要方式是使用服務的 REST API。 透過這個完整記載的端點，呼叫者可以：

* 查詢資料
* 查詢和修改中繼資料
* 擷取資料
* 查詢服務健康情況狀態
* 管理資源

不同的 Azure 資料總管服務會透過相同的公開可用 REST API 在彼此之間進行通訊。

還有許多[用戶端程式庫](client-libraries.md)可供使用服務，而不需處理 REST API 通訊協定。

## <a name="ms-tds"></a>MS-TDS

Azure 資料總管也支援 Microsoft SQL Server 通訊協定 (MS-TDS)，並包含執行 T-SQL 查詢的有限支援。 此通訊協定可讓使用者使用已知的查詢語法 (T-SQL) 及資料庫用戶端工具，例如 LINQPad、sqlcmd、Tableau、Excel 和 Power BI，在 Azure 資料總管上執行查詢。

如需詳細資訊，請參閱 [MS-TDS](tds/index.md)。

## <a name="client-libraries"></a>用戶端程式庫 

Azure 資料總管會提供許多用戶端程式庫，其利用上述端點來讓程式設計更易於存取。

* .NET SDK
* Python SDK
* R
* Java SDK
* Node SDK
* Go SDK
* PowerShell

### <a name="net-framework-libraries"></a>.NET Framework 程式庫

.NET Framework 程式庫是叫用 Azure 資料總管功能的建議方式。
可使用許多不同的程式庫。

* [Kusto.Data (Kusto 用戶端程式庫)](./netfx/about-kusto-data.md)：可以用來查詢資料、查詢中繼資料，並加以變更。 
   其建置於 Kusto REST API 之上，並將 HTTPS 要求傳送至目標 Kusto 叢集。
* [Kusto.Ingest (Kusto 內嵌程式庫)](netfx/about-kusto-ingest.md)：使用 `Kusto.Data` 並加以擴充，以輕鬆地擷取資料。

上述程式庫都會使用 Azure API，例如 Azure 儲存體 API 和 Azure Active Directory API。

### <a name="python-libraries"></a>Python 程式庫

Azure 資料總管會提供 Python 用戶端程式庫，可讓呼叫者傳送資料查詢和控制命令。
如需詳細資訊，請參閱 [Azure 資料總管 Python SDK](python/kusto-python-client-library.md)。

### <a name="r-library"></a>R 程式庫

Azure 資料總管提供 R 用戶端程式庫，可讓呼叫者傳送資料查詢和控制命令。
如需詳細資訊，請參閱 [Azure 資料總管 R SDK](r/kusto-r-client-library.md)。

### <a name="java-sdk"></a>Java SDK

JAVA 用戶端程式庫提供使用 JAVA 查詢 Azure 資料總管叢集的功能。 如需詳細資訊，請參閱 [Azure 資料總管 JAVA SDK](java/kusto-java-client-library.md)。

### <a name="node-sdk"></a>Node SDK

Azure 資料總管 Node SDK 與 Node LTS (目前為 v 6.14) 相容，並使用 ES6 建立。
如需詳細資訊，請參閱 [Azure 資料總管 Node SDK](node/kusto-node-client-library.md)。

### <a name="go-sdk"></a>Go SDK

Azure 資料總管 Go 用戶端程式庫提供使用 Go 查詢、控制和內嵌至 Azure 資料總管叢集的功能。 如需詳細資訊，請參閱 [Azure 資料總管 Golang SDK](golang/kusto-golang-client-library.md)。

### <a name="powershell"></a>PowerShell

Azure 資料總管 .NET Framework 程式庫可由 PowerShell 指令碼使用。 如需詳細資訊，請參閱[從 PowerShell 呼叫 Azure 資料總管](powershell/powershell.md)。

## <a name="monaco-ide-integration"></a>Monaco IDE 整合

`monaco-kusto` 套件支援與 Monaco Web 編輯器整合。
由 Microsoft 開發的 Monaco Editor 是 Visual Studio Code 的基礎。
如需詳細資訊，請參閱 [monaco-kusto 套件](monaco/monaco-kusto.md)。
