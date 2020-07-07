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
ms.openlocfilehash: 74871c2c1a10c199c5eb5415fcdf21590e4cf648
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967446"
---
# <a name="data-sharding-policy"></a>資料分區化原則

分區化原則會定義 Azure 資料總管叢集中的[範圍（資料分區）](../management/extents-overview.md)是否應密封。

> [!NOTE]
> 此原則適用于所有建立新範圍的作業，例如用於[資料](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands)內嵌的命令，以及[merge 和. rebuild 命令](../management/extents-commands.md#merge-extents)

資料分區化原則包含下列屬性：

- **MaxRowCount**：
    - 內嵌或重建作業所建立之範圍的最大資料列計數。
    - 預設為750000。
    - [合併作業](mergepolicy.md)**不會生效**。
        - 如果您必須限制合併作業所建立之範圍中的資料列數目，請調整 `RowCountUpperBoundForMerge` 實體[範圍合併原則](mergepolicy.md)中的屬性。
- **MaxExtentSizeInMb**：
    - 合併作業所建立之範圍的允許壓縮資料大小上限（以 mb 為單位）。
    - **僅對[合併](mergepolicy.md)作業**有效。
    - 預設為1024（1GB）。

- **MaxOriginalSizeInMb**：
    - 重建作業所建立之範圍的最大允許原始資料大小（以 mb 為單位）。
    - **只有[重建](mergepolicy.md)作業才**有效。
    - 預設為2048（2GB）。

> [!WARNING]
> 在改變數據分區化原則之前，請洽詢 Azure 資料總管小組。

建立資料庫時，它會包含預設的資料分區化原則。 資料庫中建立的所有資料表都會繼承此原則（除非在資料表層級明確覆寫此原則）。

使用[分區化原則控制命令](../management/sharding-policy.md)）來管理資料庫和資料表的資料分區化原則。
 
