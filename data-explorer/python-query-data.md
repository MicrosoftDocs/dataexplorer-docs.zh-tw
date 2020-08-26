---
title: 使用 Azure 資料總管 Python 程式庫查詢資料
description: 在本文中，您將瞭解如何使用 Python 來查詢 Azure 資料總管中的資料。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/05/2019
ms.openlocfilehash: 50e949d7ef15948dd46f5553fad8d10dad5faa96
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875050"
---
# <a name="query-data-using-the-azure-data-explorer-python-library"></a>使用 Azure 資料總管 Python 程式庫查詢資料

在本文中，您會使用 Azure 資料總管來查詢資料。 Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。

Azure 資料總管提供一個[適用於 Python 的資料用戶端程式庫](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data) \(英文\)。 此程式庫可讓您從程式碼中查詢資料。 連接到已設定協助學習 *的說明* 叢集上的資料表。 您可以查詢該叢集上的資料表，並傳回結果。

本文也會以 [Azure 筆記本](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb)的形式提供。

## <a name="prerequisites"></a>先決條件

* [Python 3.4 +](https://www.python.org/downloads/)

* 屬於 Azure Active Directory (AAD) 成員的組織電子郵件帳戶

## <a name="install-the-data-library"></a>安裝資料程式庫

安裝 *azure-kusto-data*。

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>新增 Import 陳述式和常數

從程式庫匯入類別，以及 *pandas* (資料分析程式庫)。

```python
from azure.kusto.data import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

為了驗證應用程式，Azure 資料總管會使用您的 AAD 租用戶識別碼。 若要尋找您的租用戶識別碼，請使用下列 URL，並以您的網域取代 *YourDomain*。

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

例如，如果您的網域為 *contoso.com*，則 URL 會是：[https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/)。 按一下此 URL 來查看結果；第一行如下所示。

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

在此案例中，租用戶識別碼為 `6babcaad-604b-40ac-a9d7-9fd97c0b779f`。 先設定 AAD_TENANT_ID 的值，然後再執行此程式碼。

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE = "Samples"
```

現在來建構連接字串。 此範例使用服務驗證來存取叢集。 您也可以使用 [aad 應用程式憑證](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24)、 [aad 應用程式金鑰](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20)，以及 [aad 使用者和密碼](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34)。

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>連線到 Azure 資料總管並執行查詢

針對叢集執行查詢，然後將輸出儲存於資料框架中。 此程式碼執行時，會傳回與下列類似的訊息：若要登入，請使用網頁瀏覽器開啟頁面 https://microsoft.com/devicelogin，並輸入程式碼 F3W4VWZDM 來驗證**。 請遵循下列步驟來登入，然後返回以執行下一個程式碼區塊。

```python
KUSTO_CLIENT = KustoClient(KCSB)
KUSTO_QUERY = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>在 DataFrame 中探索資料

當您進入登入之後，查詢會傳回結果，並將其儲存於資料框架中。 您可以像處理任何其他資料框架一樣來處理結果。

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

您應該會看到來自 StormEvents 資料表的前十個結果。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure 資料總管 Python 程式庫內嵌資料](python-ingest-data.md)
