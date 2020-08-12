---
title: 啟用或停用連續資料匯出-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中停用或啟用連續資料匯出。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: b69db144474557ab8d5a8e19a7bce9bbbd5df183
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149478"
---
# <a name="disable-or-enable-continuous-export"></a>停用或啟用連續匯出

停用或啟用連續匯出作業。 停用的連續匯出將不會執行，但其目前狀態會保存下來，而且可以在啟用連續匯出時繼續進行。 

當啟用已長時間停用的連續匯出時，匯出會從上次停止的位置繼續進行（當匯出已停用時）。 如果沒有足夠的叢集容量來處理所有進程，此接續作業可能會導致長時間執行的匯出，並封鎖其他匯出的執行。 連續匯出會依上次執行時間以遞增循序執行 (最舊的匯出會先執行，直到完成) 為止。 

## <a name="syntax"></a>語法

`.enable``continuous-export` *ContinuousExportName* 

`.disable``continuous-export` *ContinuousExportName* 

## <a name="properties"></a>屬性

| 屬性             | 類型   | 描述                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連續匯出的名稱 |

## <a name="output"></a>輸出

改變連續匯出的 [[顯示連續匯出] 命令](show-continuous-export.md)的結果。 
