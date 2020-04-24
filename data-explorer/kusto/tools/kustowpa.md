---
title: 庫斯托 WPA - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 Kusto WPA。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 2d8c5a9b11d8045897d8a9bd798e40ceb0f48b6a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503293"
---
# <a name="kusto-wpa"></a>庫斯托 WPA

Kusto WPA 增加了在 Windows 性能分析器 (WPA) 中執行和可視化 Kusto 查詢結果的能力。 它由兩個主要部分組成:

1. 一個啟動工具,可以接受 Kusto.Explorer 分享查詢`Share`&gt;`Query to Clipboard`(透過 ),並`.kpq`生成查詢檔 ( ), 由 WPA 處理。

1. 可以'開啟' 產生的查詢檔案的 WPA`.kpq`外掛程式 ( 。 打開此類檔會自動將檔中指定的查詢發送到 Kusto 叢集,並將結果載入,就像它們來自"標準"ETL 檔一樣。

有關詳細資訊,https://aka.ms/kustowpa請參閱 * 並下載資訊。