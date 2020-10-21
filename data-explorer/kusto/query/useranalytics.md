---
title: 使用者分析-Azure 資料總管
description: 本文說明 Azure 資料總管中的使用者分析。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/17/2019
ms.openlocfilehash: 63e19b00fef5361b1651bb1f1c88647ab8fd62fe
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248358"
---
# <a name="user-analytics-plugins"></a>使用者分析外掛程式

本節說明適用于使用者分析案例 (外掛程式) 的 Kusto 延伸模組。

|狀況|外掛程式|詳細資料|使用者體驗|
|--------|------|--------|-------|
| 計算新使用者一段時間 | [activity_counts_metrics](activity-counts-metrics-plugin.md)|傳回每個時間範圍的計數/dcounts/新計數。 每個時間範圍都會與 *所有* 先前的時間範圍比較|Kusto：報表資源庫|
| 期間週期：保留/變換率和新使用者 | [activity_metrics](activity-metrics-plugin.md)|會傳回 `dcount` 每個時間範圍的保留/流失率。 每個時間範圍與 *上一個* 時間範圍比較|Kusto：報表資源庫|
| 使用者計數和滑 `dcount` 過滑動視窗 | [sliding_window_counts](sliding-window-counts-plugin.md)|針對每個時間範圍， `dcount` 以滑動視窗的方式傳回計數和回顧期間|
| 新使用者世代：保留/流失率和新使用者 | [new_activity_metrics](new-activity-metrics-plugin.md)|比較新使用者的世代與第一次在時間範圍內看到的所有使用者 () 。 每個世代都會與所有先前的世代比較。 比較會將 *所有* 先前的時間視窗納入考慮|Kusto：報表資源庫|
|作用中使用者：相異計數 |[active_users_count](active-users-count-plugin.md)|針對每個時間範圍傳回不同的使用者。 只有在指定的回顧期間內出現至少 X 個相異期間時，才會考慮使用者。|
|使用者參與： DAU/WAU/MAU|[activity_engagement](activity-engagement-plugin.md)|在內部時間範圍之間進行比較 (例如，每日) 和外部 (例如，運算參與的每週)  (例如 DAU/WAU) |Kusto：報表資源庫|
|會話：計算作用中會話數目|[session_count](session-count-plugin.md)|計算會話在一段時間內所定義的會話計數-如果未在目前記錄的回顧期限內看到，則會將使用者記錄視為新的會話|
||||
|漏斗圖：上一個和下一個狀態順序分析 | [funnel_sequence](funnel-sequence-plugin.md)|計算已採用一系列事件的相異使用者，以及引發或跟隨順序的先前或下一個事件。 適合用來建立 [sankey 圖表](https://en.wikipedia.org/wiki/Sankey_diagram)||
|漏斗圖：順序完成分析|[funnel_sequence_completion](funnel-sequence-completion-plugin.md)|計算每個時間範圍內已 *完成* 指定序列之使用者的相異計數|
||||