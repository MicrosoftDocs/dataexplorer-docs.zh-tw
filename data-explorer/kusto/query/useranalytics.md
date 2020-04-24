---
title: 使用者分析 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的使用者分析。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/17/2019
ms.openlocfilehash: 37b5962b9441c3eb7362a239404189e489b1c3ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504976"
---
# <a name="user-analytics"></a>使用者分析

本節介紹使用者分析方案的 Kusto 擴展區(外掛程式)。

|狀況|外掛程式|詳細資料|使用者經驗|
|--------|------|--------|-------|
| 隨時間計算新使用者 | [activity_counts_metrics](activity-counts-metrics-plugin.md)|返回每個時間視窗的計數/dcounts/新計數。 每次視窗*與所有以前的時間*視窗進行比較|庫斯托.資源管理員:報表庫|
| 期間過期:保留/流失率和新使用者 | [activity_metrics](activity-metrics-plugin.md)|返回每個視窗的計數、保留/curn 速率。 將每個時間視窗與*上一*個時間視窗進行比較|庫斯托.資源管理員:報表庫|
| 使用者在滑動視窗中計數和計數 | [sliding_window_counts](sliding-window-counts-plugin.md)|對於每個時間視窗,以滑動視窗的方式在回顧期間返回計數和計數|
| 新使用者佇列:保留/流失率和新使用者 | [new_activity_metrics](new-activity-metrics-plugin.md)|比較新使用者組(在時間視窗中第一次看到的所有使用者)。 每個佇列與所有以前的佇列進行比較。 比較會考慮*所有*以前的時間視窗|庫斯托.資源管理員:報表庫|
|活動使用者:不同的計數 |[active_users_count](active-users-count-plugin.md)|為每個時間視窗返回不同的使用者,其中僅考慮使用者,如果它出現在至少 X 個不同的週期中,在 spc 化的回顧期間。|
|使用者參與度:DAU/WAU/MAU|[activity_engagement](activity-engagement-plugin.md)|比較計算參與度的內部時間視窗(例如每日)和外部(例如每周)(例如 DAU/WAU)|庫斯托.資源管理員:報表庫|
|工作階段:計算活動工作階段|[session_count](session-count-plugin.md)|計數工作階段,其中會話由時間段定義 - 使用者記錄被視為新工作階段,如果在當前記錄的回顧期間未看到它|
||||
|漏鬥:上一個和下一個狀態序列分析 | [funnel_sequence](funnel-sequence-plugin.md)|計算已執行一系列事件的不同使用者,以及引導 /後跟序列的前 v/下一個事件。 可建構[神聖圖表](https://en.wikipedia.org/wiki/Sankey_diagram)||
|漏鬥:序列完成分析|[funnel_sequence_completion](funnel-sequence-completion-plugin.md)|計算每個時間視窗*中完成*指定序列的使用者的不同計數|
||||