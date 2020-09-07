---
title: Azure 資料總管 REST API - Azure 資料總管
description: 本文說明 Azure 資料總管中的 Azure 資料總管 REST API。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: e7598ccc2c04ab54a27830ba16e2b307e84bba2c
ms.sourcegitcommit: 9e0289945270db517e173aa10024e0027b173b52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428272"
---
# <a name="azure-data-explorer-rest-api"></a>Azure 資料總管 REST API

本文說明如何透過 HTTPS 與 Azure 資料總管互動。

## <a name="supported-actions"></a>支援的動作

端點所支援的動作清單會根據端點是引擎端點或資料管理端點而有所不同。

|動作         |HTTP 指令動詞   |URI 範本           |引擎|資料管理|驗證 |
|---------------|------------|-----------------------|------|---------------|---------------|
|查詢          |GET 或 POST |/v1/rest/query         |是   |否             |是            |
|查詢          |GET 或 POST |/v2/rest/query         |是   |否             |是            |
|管理性     |POST        |/v1/rest/mgmt          |是   |是            |是            |
|UI             |GET         |/                      |是   |否             |否             |
|UI             |GET         |/{dbname}              |是   |否             |否             |

其中 [動作]  代表一組相關的活動

* [查詢] 動作會將查詢傳送至服務，並取得查詢的結果。
* [管理] 動作會將控制命令傳送至服務，並取得控制命令的結果。
* UI 動作可以用來啟動桌面用戶端或 web 用戶端。 動作是透過 HTTP 重新導向回應來完成，以便與服務互動。

## <a name="next-steps"></a>後續步驟

如需 HTTP 要求和查詢與管理動作回應的詳細資訊，請參閱：
 * [查詢管理 HTTP 要求](./request.md)
 * [查詢管理 HTTP 回應](./response.md)
 * [查詢 v2 HTTP 回應](./response2.md)

如需 UI 動作的詳細資訊，請參閱：
 * [UI 深層連結](./deeplink.md)
