---
title: 資料分區化原則-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的資料分區化原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 70d4013c8524fa88249de14a0a67cc8a85e73b3f
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337562"
---
# <a name="data-sharding-policy"></a>資料分區化原則

分區化原則會定義應如何密封 Azure 資料總管叢集中 [ (資料分區) 的範圍 ](../management/extents-overview.md) 。

> [!NOTE]
> 此原則適用于所有建立新範圍的作業，例如 [資料](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands)內嵌的命令，以及 [merge 和. rebuild 命令](./merge-extents.md)

資料分區化原則包含下列屬性：

- **MaxRowCount**：
    - 內嵌或重建作業所建立之範圍的最大資料列計數。
    - 預設為750000。
    - [合併作業](mergepolicy.md)**不會有作用**。
        - 如果您必須限制合併作業所建立之範圍內的資料列數目，請調整 `RowCountUpperBoundForMerge` 實體 [範圍合併原則](mergepolicy.md)中的屬性。
- **MaxExtentSizeInMb**：
    - 合併作業所建立之範圍的允許壓縮資料大小上限 (mb) 。
    - **只適用于[合併](mergepolicy.md)作業**。
    - 預設為 1024 (1GB) 。

- **MaxOriginalSizeInMb**：
    - 針對重建作業所建立的範圍，允許的原始資料大小上限 (mb) 。
    - **只適用于[重建](mergepolicy.md)作業**。
    - 預設為 2048 (2GB) 。

> [!WARNING]
> 變更資料分區化原則之前，請洽詢 Azure 資料總管團隊。

建立資料庫時，它會包含預設的資料分區化原則。 除非在資料表層級上明確地覆寫此原則，否則在資料庫 (中建立的所有資料表都會繼承此原則) 。

使用 [分區化原則控制命令](../management/sharding-policy.md)) 管理資料庫和資料表的資料分區化原則。
