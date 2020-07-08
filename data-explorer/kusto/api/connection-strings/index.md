---
title: 連接字串 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的連接字串。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 9cea718db64c3da998df8b832d886ebd87d0f241
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058757"
---
# <a name="connection-strings"></a>連接字串

連接字串廣泛用於 Kusto 控制命令、Kusto API，有時也會在查詢中使用。
連接字串提供了一般的方法來描述如何尋找 Kusto 外部資源並與其互動，例如 Azure Blob 儲存體服務和 Azure SQL Database 資料庫中的 Blob。

有一些連接字串格式：

* [Kusto 連接字串](./kusto.md)描述如何與 Kusto 服務端點進行通訊。
  Kusto 連接字串會在 [ADO.NET 連接字串模型](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax)之後進行模型化。
* [儲存體連接字串](./storage.md)描述如何將 Kusto 指向外部儲存體服務，例如 Azure Blob 儲存體和 Azure Data Lake Storage。
* SQL 連接字串 - 由 Kusto [sql_request 外掛程式](../../query/sqlrequestplugin.md)用來向 Azure DB 服務發出要求，並[匯出至 SQL 命令](../../management/data-export/export-data-to-sql.md)。  
  這些連接字串會遵守 [SqlClient 連接字串](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)規格。

> [!NOTE]
> 某些連接字串可能會參考安全性主體。 如需如何在連接字串中指定安全性主體的詳細資訊，請參閱 [主體和識別提供者](../../management/access-control/principals-and-identity-providers.md)。
