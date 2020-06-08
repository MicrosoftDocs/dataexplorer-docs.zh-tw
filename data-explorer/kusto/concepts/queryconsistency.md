---
title: 查詢一致性-Azure 資料總管
description: 本文說明 Azure 資料總管中的查詢一致性。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 8b4d1df4dc9a035764f9d50a4f9c4dcf452da67e
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512260"
---
# <a name="query-consistency"></a>查詢一致性

Kusto 支援兩種查詢一致性模型：**強**式和**弱**式。

強式一致查詢（預設值）具有「讀取-我的變更」保證。 如果您傳送 control 命令並收到命令已成功完成的通知，您將會保證緊接在之後的任何查詢都會觀察到命令的結果。

用戶端必須明確啟用的弱式一致查詢，並不一定要這麼做。 進行查詢的用戶端可能會在反映這些變更的變更與查詢之間發現一些延遲（通常是1-2 分鐘）。

弱式一致查詢的優點是，它可減少處理資料庫變更的叢集節點上的負載。 一般來說，我們建議您先嘗試強的一致性模型。 只有在必要時，才改用弱式一致查詢。

若要切換到弱式一致查詢，請 `queryconsistency` 在進行[REST API 呼叫](../api/rest/request.md)時設定屬性。 .NET 用戶端的使用者也可以在[Kusto 連接字串](../api/connection-strings/kusto.md)中，或在[用戶端要求屬性](../api/netfx/request-properties.md)中設定為旗標。
