---
title: 窄外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的窄外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75b211f32c15eefc60ca40b0408345be4a656652
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512235"
---
# <a name="narrow-plugin"></a>窄外掛程式

```kusto
T | evaluate narrow()
```

`narrow`外掛程式將寬表"解點"到只有三列的表中:行號、列類型和列值(`string`作為 )。

該`narrow`外掛程式主要用於顯示目的,因為它允許寬桌舒適地顯示,而無需水平滾動。

**語法**

`T | evaluate narrow()`

**範例**

下面的範例顯示了讀取 Kusto`.show diagnostics`控制命令輸出的簡便方法。

```kusto
.show diagnostics
 | evaluate narrow()
```

其結果是`.show diagnostics`具有一行和 33 列的表。 使用外掛程式,`narrow`我們將輸出「旋轉」 到類似這樣的內容:

資料列  | 資料行                              | 值
-----|-------------------------------------|-----------------------------
0    | 是健康的                           | True
0    | 需要重新平衡                 | False
0    | 需要進行尺規延伸                  | False
0    | 機器總計                       | 2
0    | 機器離線                     | 0
0    | 節點上次重新啟動                 | 2017-03-14 10:59:18.9263023
0    | 管理員上次選舉on                  | 2017-03-14 10:58:41.6741934
0    | 叢集暖資料容量因數       | 0.130552847673333
0    | 範圍總計                        | 136
0    | 磁碟冷分配百分比        | 5
0    | 實體基礎基礎資料容量  | 2
0    | 原始資料總大小               | 5167628070
0    | 總範圍大小                     | 1779165230
0    | IngestionsLoadFactor                | 0
0    | 在進步中攝錄                | 0
0    | 攝入成功率               | 100
0    | 合併進度                    | 0
0    | BuildVersion                        | 1.0.6281.19882
0    | 組建時間                           | 2017-03-13 11:02:44.0000000
0    | ClusterDataCapacityFactor           | 0.130552847673333
0    | 需要資料加熱               | False
0    | 重新平衡上流                  | 2017-03-21 09:14:53.8523455
0    | 資料加熱LastRunon                | 2017-03-21 09:19:54.1438800
0    | 合併成功率                   | 100
0    | 不健康的原因                    | [空]
0    | 需要注意                 | False
0    | 需要注意的原因             | [空]
0    | ProductVersion                      | KustoRelease_2017.03.13.2
0    | 失敗動作              | 0
0    | 失敗的合併作業               | 0
0    | 最大範圍在單一表中             | 64
0    | 具有最大範圍的表                 | 庫斯托監控持久資料庫.庫斯托監控表
0    | 暖度大小                      | 1779165230