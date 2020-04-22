---
title: Azure 資料總管 API 概觀 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的 Azure 資料總管 API 概觀。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: c791ae0be0c895a4744e761c58e7d455aa0a22b9
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610270"
---
# <a name="azure-data-explorer-api-overview"></a>Azure 資料總管 API 概觀

Azure 資料總管服務支援下列通訊端點：

1. [REST API](#rest-api) 端點，您可以透過該端點查詢和管理 Azure 資料總管中的資料。
   此端點支援查詢和[控制命令](../management/index.md)的 [Kusto 查詢語言](../query/index.md)。
2. [MS-TDS](#ms-tds) 端點，其會實作由 Microsoft SQL Server 產品所使用的 Microsoft 表格式資料流 (TDS) 通訊協定子集。
   這個端點主要適用於知道如何與 SQL Server 端點通訊以進行查詢的現有工具。
3. [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftkusto) 端點，這是 Azure 服務用於管理資源 (例如 Azure 資料總管叢集) 的標準方法。

Azure 資料總管會提供許多用戶端程式庫，它們利用上述端點來讓程式設計更易於存取：

1. .NET SDK
2. Python SDK
3. Java SDK
4. Node SDK
5. Go SDK
6. PowerShell
7. R

## <a name="rest-api"></a>REST API

與任何 Azure 資料總管服務通訊的主要方式是使用服務的 REST API。 透過這個完整記載的端點，呼叫端可以：

1. 查詢資料
2. 查詢和修改中繼資料
3. 擷取資料
4. 查詢服務健康情況狀態
5. 管理資源

不同的 Azure 資料總管服務會使用相同的公開可用 REST API 在彼此之間進行通訊。

除了支援使用 REST API 對 Azure 資料總管提出要求，還有許多用戶端程式庫可供使用服務，而不需處理 REST API 通訊協定。

## <a name="ms-tds"></a>MS-TDS

另一種連線到 Azure 資料總管並查詢其資料的方式，就是 Azure 資料總管支援 Microsoft SQL Server 通訊協定 (MS-TDS)，並包含執行 T-SQL 查詢的有限支援。 這可讓使用者使用已知的查詢語法 (T-SQL) 及其熟悉的資料庫用戶端工具 (例如 LINQPad、sqlcmd、Tableau、Excel 和 Power BI)，在 Azure 資料總管上執行查詢

您可以在[此頁面](tds/index.md)找到更多關於 MS-TDS 的詳細資料。

## <a name="net-framework-libraries"></a>.NET Framework 程式庫

.NET Framework 程式庫是叫用 Azure 資料總管功能的建議方式。
提供許多不同的程式庫：

- [**Kusto.Data (Kusto 用戶端程式庫)** ](./netfx/about-kusto-data.md)，可用來查詢資料、查詢中繼資料，並加以變更。
- [**Kusto.Ingest (Kusto 內嵌程式庫)** ](netfx/about-kusto-ingest.md)，其會使用 Kusto.Data 並加以擴充來輔助資料內嵌。


**Kusto 用戶端程式庫** (Kusto.Data) 建置於 Kusto REST API 之上，並將 HTTPS 要求傳送至目標 Kusto 叢集。 

**Kusto 內嵌程式庫** (Kusto.Ingest) 會利用 Kusto.Data。



上述所有程式庫都會使用 Azure API (例如 Azure 儲存體 API、Azure Active Directory API)。

## <a name="python-libraries"></a>Python 程式庫

Azure 資料總管會提供 Python 用戶端程式庫，可讓呼叫端傳送資料查詢和控制命令。

## <a name="r-library"></a>R 程式庫

Azure 資料總管提供 R 用戶端程式庫，可讓呼叫端傳送資料查詢和控制命令。



## <a name="using-azure-data-explorer-from-powershell"></a>從 PowerShell 使用 Azure 資料總管

Azure 資料總管 .NET Framework 程式庫可由 PowerShell 指令碼使用。
[從 PowerShell 呼叫 Azure 資料總管](powershell/powershell.md)提供範例。

## <a name="ide-integration"></a>IDE 整合

`monaco-kusto` 套件支援與 Monaco Editor 整合，這是由 Microsoft 開發的 Web 編輯器，而且是 Visual Studio Code 的基礎。
深入瞭解 [monaco-kusto](monaco/monaco-kusto.md) 套件。