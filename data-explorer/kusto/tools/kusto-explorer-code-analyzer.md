---
title: 函式庫斯托資源管理員碼分析器 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 Kusto 資源管理員代碼分析器。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 246f047a7276bbb403598ff45b4c84f157e549d8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524186"
---
# <a name="kusto-explorer-code-analyzer"></a>函式庫斯托資源管理員碼分析器

Kusto.Explorer 提供代碼分析器實用程式,用於分析當前查詢並輸出一組適用的改進建議。 

`Ctrl`用於`F6`在活動查詢上+運行分析器。

![alt 文字](./Images/KustoTools-KustoExplorer/ke-codeanalyze.gif "程式碼分析器參考")