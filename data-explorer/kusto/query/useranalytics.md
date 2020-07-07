---
title: 使用者分析-Azure 資料總管
description: 本文說明 Azure 資料總管中的使用者分析。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/17/2019
ms.openlocfilehash: 5a9ca6259296f2fa2c5ad83622e7f3012169864e
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966970"
---
# <a name="user-analytics-plugins"></a>使用者分析外掛程式

本節說明使用者分析案例的 Kusto 延伸模組（外掛程式）。

|案例|外掛程式|詳細資料|使用者體驗|
|--------|------|--------|-------|
| 一段時間內的新使用者計數 | [activity_counts_metrics](activity-counts-metrics-plugin.md)|傳回每個時間範圍的計數/dcounts/新計數。 每個時間範圍都會與先前*所有*的時間視窗比較|Kusto. Explorer：報表圖庫|
| 週期-一段期間：保留/變換率和新使用者 | [activity_metrics](activity-metrics-plugin.md)|傳回 `dcount` ，每個時間範圍的保留/變換率。 每個時間範圍與*上一個*時間範圍的比較|Kusto. Explorer：報表圖庫|
| 使用者計數和 `dcount` 滑過時間範圍 | [sliding_window_counts](sliding-window-counts-plugin.md)|針對每個時間範圍， `dcount` 以滑動視窗的方式傳回計數和回顧期間|
| 新使用者世代：保留/變換率和新使用者 | [new_activity_metrics](new-activity-metrics-plugin.md)|比較新使用者的世代（在時間範圍內第一次看到的所有使用者）。 每個世代都會與所有先前的世代比較。 比較會將*所有*先前的時間視窗納入考慮|Kusto. Explorer：報表圖庫|
|作用中使用者：相異計數 |[active_users_count](active-users-count-plugin.md)|針對每個時間範圍傳回不同的使用者。 只有在指定的回顧期間內，使用者才會被視為至少 X 個相異的期間。|
|使用者參與： DAU/WAU/MAU|[activity_engagement](activity-engagement-plugin.md)|比較內部時間範圍（例如，每日）和外部（例如，每週）進行運算的參與（例如，DAU/WAU）|Kusto. Explorer：報表圖庫|
|會話：計算作用中會話計數|[session_count](session-count-plugin.md)|計數會話，其中會話是由時間週期所定義-使用者記錄會被視為新的會話（如果它在目前記錄的回顧期間內尚未看到）|
||||
|漏斗圖：上一個和下一個狀態順序分析 | [funnel_sequence](funnel-sequence-plugin.md)|計算已採取一系列事件的不同使用者，以及引發或後面接順序的上一個或下一個事件。 適用于建立[sankey 圖表](https://en.wikipedia.org/wiki/Sankey_diagram)||
|漏斗圖：序列完成分析|[funnel_sequence_completion](funnel-sequence-completion-plugin.md)|計算每個時間範圍內已*完成*指定順序的使用者相異計數|
||||