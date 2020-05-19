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
ms.openlocfilehash: a128db995c78c0583bc7c7712c06292a2f6598d1
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550532"
---
# <a name="ms-tds-t-sql-support"></a>MS-TDS T-SQL 支援

Azure 資料總管 (Kusto) 支援一小組 Microsoft SQL Server 通訊協定 (MS-TDS)，其中包含 T-SQL 查詢語言的子集。 Microsoft Excel 和 Microsoft Power BI 只是可與 Azure 資料總管 (Kusto) 搭配使用的許多工具之一。 這些 Microsoft 應用程式也知道如何查詢 SQL Server。

> [!NOTE]
> 由於用戶端工具透過 MS-TDS 查詢 Kusto，用戶端必須使用 Azure Active Directory (Azure AD) 整合式驗證。

## <a name="next-steps"></a>後續步驟

* [T-SQL](./t-sql.md) - 深入了解由 Kusto 所執行的 T-SQL 查詢語言。 

* [KQL over TDS](./tdskql.md) - 深入了解透過 TDS 端點執行原生 KQL 查詢。

* [MS-TDS 用戶端和 Kusto](./clients.md) - 從使用 MS-TDS/T-SQL 的知名用戶端使用 Azure 資料總管。

* [Azure 資料總管 (Kusto) 作為 SQL 伺服器的連結伺服器](./linkedserver.md) - 將叢集設定為 SQL 伺服器內部部署的連結伺服器。 

* [具有 Azure Active Directory 的 MS-TDS](./aad.md) - 透過 TDS 使用 Azure AD，以連接到 Azure 資料總管。

* [SQL 已知問題](./sqlknownissues.md) 深入了解 T-SQL 與 Azure 資料總管的 SQL Server 實作之間的一些主要差異。
