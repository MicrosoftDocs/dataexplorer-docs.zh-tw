---
title: MS-TDS T-SQL 支援 - Azure 資料總管
description: 本文介紹 Azure 資料總管中的 MS-TDS T-SQL 支援。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: cc10fcc725e038d6428d4b794a1f6d368a86a39e
ms.sourcegitcommit: 9e0289945270db517e173aa10024e0027b173b52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428408"
---
# <a name="ms-tds-t-sql-support"></a>MS-TDS T-SQL 支援

Azure 資料總管支援一小組 Microsoft SQL Server 通訊協定 (MS-TDS)，其中包含 T-SQL 查詢語言的子集。 Microsoft Excel 和 Microsoft Power BI 只是可與 Azure 資料總管搭配使用的許多工具之一。 這些 Microsoft 應用程式也知道如何查詢 SQL Server。

> [!NOTE]
> 由於用戶端工具透過 MS-TDS 查詢 Kusto，用戶端必須使用 Azure Active Directory (Azure AD) 整合式驗證。

## <a name="next-steps"></a>後續步驟

* [T-SQL](./t-sql.md) - 深入了解由 Kusto 所執行的 T-SQL 查詢語言。 

* [KQL over TDS](./tdskql.md) - 深入了解透過 TDS 端點執行原生 KQL 查詢。

* [MS-TDS 用戶端和 Kusto](./clients.md) - 從使用 MS-TDS/T-SQL 的知名用戶端使用 Azure 資料總管。

* [Azure 資料總管作為 SQL 伺服器的連結伺服器](./linkedserver.md) - 將叢集設定為 SQL 伺服器內部部署的連結伺服器。 

* [具有 Azure Active Directory 的 MS-TDS](./aad.md) - 透過 TDS 使用 Azure AD，以連接到 Azure 資料總管。

* [SQL 已知問題](./sqlknownissues.md) 深入了解 T-SQL 與 Azure 資料總管的 SQL Server 實作之間的一些主要差異。
