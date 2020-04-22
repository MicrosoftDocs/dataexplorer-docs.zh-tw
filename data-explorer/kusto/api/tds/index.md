---
title: MS-TDS (T-SQL 支援) - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的 MS-TDS (T-SQL 支援)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: 8aaea26b6c4e8a7f76c4129faeb681791f25ae7e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490543"
---
# <a name="ms-tds-t-sql-support"></a>MS-TDS (T-SQL 支援)

Kusto 支援一小組 Microsoft SQL Server 通訊協定 (MS-TDS)，，其中包含 T-SQL 查詢語言的子集，讓知道如何查詢 SQL Server 的現有工具可搭配 Kusto 使用。 支援的用戶端包括 Microsoft Excel、Microsoft Power BI 及其他許多用戶端。

請注意，若要讓用戶端工具透過 MS-TDS 查詢 Kusto，用戶端必須使用 Azure Active Directory 整合式驗證。

如需 Kusto 所執行 T-SQL 查詢語言的詳細資訊，請參閱 [T-SQL](./t-sql.md)。 

請參閱 [MS-TDS 用戶端和 Kusto](./clients.md)，以取得如何從使用 MS-TDS/T-SQL 的某些知名用戶端使用 Kusto 的範例。

請參閱 [Kusto 作為 SQL 伺服器的連結伺服器](./linkedserver.md)，將 Kusto cluster 設定為 SQL 伺服器內部部署的連結伺服器。

如需透過 TDS 使用 AAD 連線到 Kusto 的詳細資訊，請參閱[使用 Azure Active Directory 的 MS-TDS](./aad.md)。

如需透過 TDS 端點執行原生 KQL 查詢的相關資訊，請參閱 [KQL over TDS](./tdskql.md)。 

最後，請參閱[此內容](./sqlknownissues.md)，以了解 T-SQL 與 Kusto 的 SQL Server 實作之間的一些主要差異。