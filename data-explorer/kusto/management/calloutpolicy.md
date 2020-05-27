---
title: 標注原則-Azure 資料總管
description: 本文說明 Azure 資料總管中的標注原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 42254e00e629a19dfceeef2d4a6c2d1877400c05
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011545"
---
# <a name="callout-policy"></a>註標原則

在許多不同的案例中，Azure 資料總管叢集可以與外部服務進行通訊。
叢集系統管理員可以藉由更新叢集的注標原則，來管理外部呼叫的授權網域。

注標原則會在叢集層級進行管理，並分類為下列類型。
* `kusto`-控制 Azure 資料總管跨叢集查詢。
* `sql`-控制[SQL 外掛程式](../query/sqlrequestplugin.md)。

* `webapi`-控制其他外部 Web 呼叫。
* `sandbox_artifacts`-控制沙箱化外掛程式（[python](../query/pythonplugin.md)  |  [R](../query/rplugin.md)）。

注標原則是由下列各項所組成。

* **CalloutType** -定義標注的類型，可以是 `kusto` 、 `sql` 或。`webapi`
* **CalloutUriRegex** -指定注標網域的允許 Regex
* **CanCall** -指出是否允許外部呼叫的標注。

## <a name="predefined-callout-policies"></a>預先定義的標注原則

此表格顯示一組預先定義的注標原則，預先設定在所有 Azure 資料總管叢集上，以啟用標注來選取服務。

|服務      |Cloud        |指定  |允許的網域 |
|-------------|-------------|-------------|-------------|
|Kusto |`Public Azure` |跨叢集查詢 |`^[^.]*\.kusto\.windows\.net$` <br> `^[^.]*\.kustomfa\.windows\.net$` |
|Kusto |`Black Forest` |跨叢集查詢 |`^[^.]*\.kusto\.cloudapi\.de$` <br> `^[^.]*\.kustomfa\.cloudapi\.de$` |
|Kusto |`Fairfax` |跨叢集查詢 |`^[^.]*\.kusto\.usgovcloudapi\.net$` <br> `^[^.]*\.kustomfa\.usgovcloudapi\.net$` |
|Kusto |`Mooncake` |跨叢集查詢 |`^[^.]*\.kusto\.chinacloudapi\.cn$` <br> `^[^.]*\.kustomfa\.chinacloudapi\.cn$` |
|Azure DB |`Public Azure` |SQL 要求 |`^[^.]*\.database\.windows\.net$` <br> `^[^.]*\.databasemfa\.windows\.net$` |
|Azure DB |`Black Forest` |SQL 要求 |`^[^.]*\.database\.cloudapi\.de$` <br> `^[^.]*\.databasemfa\.cloudapi\.de$` |
|Azure DB |`Fairfax` |SQL 要求 |`^[^.]*\.database\.usgovcloudapi\.net$` <br> `^[^.]*\.databasemfa\.usgovcloudapi\.net$` |
|Azure DB |`Mooncake` |SQL 要求 |`^[^.]*\.database\.chinacloudapi\.cn$` <br> `^[^.]*\.databasemfa\.chinacloudapi\.cn$` |
|基準服務 |公用 Azure |基準化要求 |`baseliningsvc-int.azurewebsites.net` <br> `baseliningsvc-ppe.azurewebsites.net` <br> `baseliningsvc-prod.azurewebsites.net` |

## <a name="control-commands"></a>控制命令

命令需要[AllDatabasesAdmin](access-control/role-based-authorization.md)許可權。

**顯示所有已設定的標注原則**

```kusto
.show cluster policy callout
```

**改變標注原則**

```kusto
.alter cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}]'
```

**新增一組允許的標注**

```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}, {"CalloutType": "webapi","CalloutUriRegex": "bing\\.com","CanCall": true}]'
```

**刪除所有不可變的標注原則**

```kusto
.delete cluster policy callout
```
