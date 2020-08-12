---
title: 捨棄連續資料匯出-Azure 資料總管
description: 本文說明如何卸載 Azure 資料總管中的連續資料匯出。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: f08d8c99c242aa63e3847d3965bf9511967b60e1
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149460"
---
# <a name="drop-continuous-export"></a>捨棄連續匯出

卸載連續匯出作業。

## <a name="syntax"></a>語法

`.drop``continuous-export` *ContinuousExportName*

## <a name="properties"></a>屬性

| 屬性             | 類型   | 描述                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連續匯出的名稱 |

## <a name="output"></a>輸出

資料庫中剩餘的連續匯出 (刪除後的) 。 輸出架構，如同 [ [顯示連續匯出] 命令](show-continuous-export.md)。
