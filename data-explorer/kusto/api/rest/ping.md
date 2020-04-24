---
title: 庫斯頓平 REST API - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 資料資源管理器中的 Kusto Ping REST API。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 14a3d3dd641ff435784eac4e813d98bac8c4a552
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502579"
---
# <a name="kusto-ping-rest-api"></a>庫斯頓平 REST API

提供 ping REST API 用於允許負載均衡器等網路設備驗證群集無狀態前端元件的簡單網路回應。 當 GET 謂詞應用於此終結點時,群集通過返回 200 OK HTTP 回應進行回應。 REST API 未經過身份驗證(呼叫者不需要`Authorization`發送 HTTP 標頭)。

- 路徑：`/v1/rest/ping`
- 動詞:`GET`
- 操作:透過訊息回應`200 OK`
- 參數:無