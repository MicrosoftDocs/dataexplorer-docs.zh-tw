---
title: 標註策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的標註策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: df57c4901cef74d574108d2c6e75672d1faba75c
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744507"
---
# <a name="callout-policy"></a>註標原則

## <a name="overview"></a>概觀

Azure 資料資源管理器群集可以在許多不同的方案中與外部服務通信。
群集管理員可以通過更新群集的標註策略來管理外部呼叫的允許域。

標註策略在群集等級進行管理,並分為以下類型:
* `kusto`- 控制 Azure 資料資源管理器跨群集查詢。
* `sql`- 控制[SQL 外掛程式](../query/sqlrequestplugin.md)。


* `webapi`- 控制其他外部 Web 調用。
* `sandbox_artifacts`- 控制沙箱外掛程式[(python](../query/pythonplugin.md) | [R](../query/rplugin.md))。

標註原則由以下元件組成:
* **標註型態**定義標註的類型,可以是以下類型之一:kusto、sql 或 webapi
* **標記網的 UriRegex**指定標記網域的允許的 Regex
* **CanCall**指示標註是否允許外部呼叫。

## <a name="predefined-callout-policies"></a>預先定義的標註原則

在所有 Azure 資料資源管理器群集上,有一組預定義的標註策略,不可改變地預配置,以方便標註選擇服務。

|服務      |雲端        |指定  |允許的網域 |
|-------------|-------------|-------------|-------------|
|Kusto |公用 Azure |跨群集查詢 |`^[^.]*\.kusto\.windows\.net$` <br> `^[^.]*\.kustomfa\.windows\.net$` |
|Kusto |黑森林 |跨群集查詢 |`^[^.]*\.kusto\.cloudapi\.de$` <br> `^[^.]*\.kustomfa\.cloudapi\.de$` |
|Kusto |Fairfax |跨群集查詢 |`^[^.]*\.kusto\.usgovcloudapi\.net$` <br> `^[^.]*\.kustomfa\.usgovcloudapi\.net$` |
|Kusto |月餅 |跨群集查詢 |`^[^.]*\.kusto\.chinacloudapi\.cn$` <br> `^[^.]*\.kustomfa\.chinacloudapi\.cn$` |
|Azure DB |公用 Azure |SQL 要求 |`^[^.]*\.database\.windows\.net$` <br> `^[^.]*\.databasemfa\.windows\.net$` |
|Azure DB |黑森林 |SQL 要求 |`^[^.]*\.database\.cloudapi\.de$` <br> `^[^.]*\.databasemfa\.cloudapi\.de$` |
|Azure DB |Fairfax |SQL 要求 |`^[^.]*\.database\.usgovcloudapi\.net$` <br> `^[^.]*\.databasemfa\.usgovcloudapi\.net$` |
|Azure DB |月餅 |SQL 要求 |`^[^.]*\.database\.chinacloudapi\.cn$` <br> `^[^.]*\.databasemfa\.chinacloudapi\.cn$` |
|基線服務 |公用 Azure |基線要求 |`baseliningsvc-int.azurewebsites.net` <br> `baseliningsvc-ppe.azurewebsites.net` <br> `baseliningsvc-prod.azurewebsites.net` |


## <a name="control-commands"></a>控制命令

這些命令需要[所有資料庫管理員](access-control/role-based-authorization.md)許可權。

**顯示所有設定的標記原則**

```kusto
.show cluster policy callout
```

**變更宣告標註原則**

```kusto
.alter cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}]'
```

**新增一組允許的標註**

```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}, {"CalloutType": "webapi","CalloutUriRegex": "bing\\.com","CanCall": true}]'
```

**移除所有不可變更的標註原則**

```kusto
.delete cluster policy callout
```
