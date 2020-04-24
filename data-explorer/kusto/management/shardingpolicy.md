---
title: 資料分片策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的數據分片策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 6ed9cf3a1667fb57271a44dcfe7c6dd5318c4010
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520021"
---
# <a name="data-sharding-policy"></a>資料分片原則

分片策略定義是否以及如何密封 Azure 資料資源管理器群集中的[範圍(資料分片)。](../management/extents-overview.md)

> [!NOTE]
> 該策略應用於建立新擴充碟區的所有作業,例如[資料引入](../management/data-ingestion/index.md)的指令和[.merge 和 .rebuild)命令](../management/extents-commands.md#merge-extents)

資料分片原則包含以下屬性:

- **最大羅計數**:
    - 由引入或重建操作創建的擴展盤區的最大行計數。
    - 默認值為 750,000。
    - **對**[合併操作](mergepolicy.md)無效。
        - 如果必須限制合併操作創建的擴展區中的行數,請調整實體[區](mergepolicy.md)合併策略`RowCountUpperBoundForMerge`中的屬性。
- **最大範圍SizeInMb**:
    - 合併操作創建的擴展盤區允許的最大壓縮數據大小(以兆位元組為單位)。
    - **只適用於[合併](mergepolicy.md)操作**。
    - 默認值為 1,024 (1GB)。

- **最大原始大小InMb**:
    - 對於重建操作創建的擴展盤區,允許的最大原始數據大小(以兆位元組為單位)。
    - 您**可以重建[rebuild](mergepolicy.md)。**
    - 默認值為 2,048 (2GB)。

> [!WARNING]
> 在更改數據分片策略之前,請諮詢 Azure 資料資源管理器團隊。

創建資料庫時,它包含預設數據分片策略。 此策略由資料庫中創建的所有表繼承(除非在表級別顯式重寫該策略)。

使用[分片策略控制命令](../management/sharding-policy.md)管理資料庫和表的數據分片策略。
 