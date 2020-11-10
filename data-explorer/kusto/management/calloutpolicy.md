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
ms.openlocfilehash: da1cca764563b4ad2ce96ceaeb117e33d059303d
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94417603"
---
# <a name="callout-policy"></a>註標原則

在許多不同的案例中，Azure 資料總管叢集可以與外部服務進行通訊。
叢集系統管理員可以藉由更新叢集的注標原則來管理外部呼叫的授權網域。

注標原則會在叢集層級進行管理，並分類為下列類型。
* `kusto` -控制 Azure 資料總管跨叢集查詢。
* `sql` -控制 [SQL 外掛程式](../query/sqlrequestplugin.md) 和 [mysql_request 外掛程式](../query/mysqlrequest-plugin.md)。
* `cosmosdb` -控制 [CosmosDB 外掛程式](../query/cosmosdb-plugin.md)。
* `sandbox_artifacts`-控制 ([python](../query/pythonplugin.md)  |  [R](../query/rplugin.md)) 的沙箱化外掛程式。
* `external_data` -透過 [外部資料表](../query/schema-entities/externaltables.md) 或 [externaldata](../query/externaldata-operator.md) 運算子控制外部資料的存取。

注標原則是由下列各項所組成。

* **CalloutType** -定義標注的類型，而且可以是 `kusto` 或 `sql` 。
* **CalloutUriRegex** -指定標注網域的允許 Regex
* **CanCall** -指出是否允許外部呼叫標注。

## <a name="predefined-callout-policies"></a>預先定義的標注原則

表格會顯示一組預先定義的注標原則，這些原則會在所有 Azure 資料總管叢集上預先設定，以啟用標注來選取服務。

|服務      |Cloud        |名稱  |允許的網域 |
|-------------|-------------|-------------|-------------|
|Kusto |`Public Azure` |跨叢集查詢 |`^[^.]*\.kusto\.windows\.net$` <br> `^[^.]*\.kustomfa\.windows\.net$` |
|Kusto |`Black Forest` |跨叢集查詢 |`^[^.]*\.kusto\.cloudapi\.de$` <br> `^[^.]*\.kustomfa\.cloudapi\.de$` |
|Kusto |`Fairfax` |跨叢集查詢 |`^[^.]*\.kusto\.usgovcloudapi\.net$` <br> `^[^.]*\.kustomfa\.usgovcloudapi\.net$` |
|Kusto |`Mooncake` |跨叢集查詢 |`^[^.]*\.kusto\.chinacloudapi\.cn$` <br> `^[^.]*\.kustomfa\.chinacloudapi\.cn$` |
|Azure DB |`Public Azure` |SQL 要求 |`^[^.]*\.database\.windows\.net$` <br> `^[^.]*\.databasemfa\.windows\.net$` |
|Azure DB |`Black Forest` |SQL 要求 |`^[^.]*\.database\.cloudapi\.de$` <br> `^[^.]*\.databasemfa\.cloudapi\.de$` |
|Azure DB |`Fairfax` |SQL 要求 |`^[^.]*\.database\.usgovcloudapi\.net$` <br> `^[^.]*\.databasemfa\.usgovcloudapi\.net$` |
|Azure DB |`Mooncake` |SQL 要求 |`^[^.]*\.database\.chinacloudapi\.cn$` <br> `^[^.]*\.databasemfa\.chinacloudapi\.cn$` |
|基準服務 |公用 Azure |基準要求 |`baseliningsvc-int.azurewebsites.net` <br> `baseliningsvc-ppe.azurewebsites.net` <br> `baseliningsvc-prod.azurewebsites.net` |

## <a name="control-commands"></a>控制命令

這些命令需要 [AllDatabasesAdmin](access-control/role-based-authorization.md) 許可權。

**顯示所有設定的標注原則**

```kusto
.show cluster policy callout
```

**Alter callout 原則**

```kusto
.alter cluster policy callout @'[{"CalloutType": "sql","CalloutUriRegex": "sqlname.database.azure.com","CanCall": true}]'
```

**新增一組允許的標注**

```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "sql","CalloutUriRegex": "sqlname.database.azure.com","CanCall": true}]'
```

**刪除所有不可變的標注原則**

```kusto
.delete cluster policy callout
```
