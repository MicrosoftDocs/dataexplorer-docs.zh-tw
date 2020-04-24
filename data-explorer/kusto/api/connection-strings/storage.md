---
title: 儲存連線字串 ─ Azure 資料資源管理員 ( Azure) 的管理員 。微軟文件
description: 本文介紹 Azure 資料資源管理員中的儲存連接字串。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 909198e42786666d3a26874e18b5cfd57b27ab1f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502953"
---
# <a name="storage-connection-strings"></a>儲存體連接字串

一些 Kusto 命令指示 Kusto 與外部儲存服務進行互動。 例如,可以告訴 Kusto 將資料匯出到 Azure 儲存 Blob,在這種情況下,需要提供特定的參數(如存儲帳戶名稱或 blob 容器)。

Kusto 支援以下儲存提供者:


* Azure 儲存 Blob 儲存提供者
* Azure 資料湖儲存儲存提供者

每種存儲提供者都定義用於描述儲存資源以及如何存取這些資源的連接字串格式。
Kusto 使用 URI 格式來描述這些儲存資源以及存取這些資源所需的屬性(如安全認證)。


|提供者                   |配置    |URI 範本                          |
|---------------------------|----------|--------------------------------------|
|Azure 儲存體 Blob         |`https://`|`https://`*帳號**容器*`/`[*Blob 名稱*`?`]*sasKey* \| `;`*帳戶金鑰*|`.blob.core.windows.net/`|
|Azure Data Lake Store Gen 2|`abfss://`|`abfss://`*檔案系統*`@`*Account*帳號`.dfs.core.windows.net/`*路徑到目錄或檔案*[`;`*呼叫者認證*|
|Azure Data Lake Store Gen 1|`adl://`  |`adl://`*帳號*.azuredatalakestore.net/*路徑目錄檔*[`;`*呼叫者認證*|

## <a name="azure-storage-blob"></a>Azure 儲存體 Blob

此提供程式是最常用的,在所有方案中都支援它。
訪問資源時,必須向提供程式授予憑據。 提供認證有兩個受支援的機制:

* 使用 Azure 儲存 Blob 的`?sig=...`標準查詢 () 提供共享存取 (SAS) 密鑰。 當 Kusto 需要存取資源有限時,請使用此方法。
* 提供儲存帳號金鑰(`;ljkAkl...==`。 當 Kusto 需要持續存取資源時,請使用此方法。

範例(請注意,這顯示模糊字串文字,以便不公開帳戶金鑰或 SAS):

`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv;<storage_account_key_text, ends with '=='>"`
`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv?sv=...&sp=rwd"` 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="azure-data-lake-store-gen-2"></a>Azure Data Lake Store Gen 2

此提供程式支援存取 Azure 資料湖儲存第 2 代中的數據。

URI 格式為:

`abfss://`*檔案系統*`@`*儲存帳戶名稱*`.dfs.core.windows.net/`*路徑*`;`*呼叫方認證*

其中：

* _檔案系統_是 ADLS 檔案系統物件名稱(大致相當於 Blob 容器)
* _儲存帳號名稱_是儲存帳戶的名稱
* _路徑_是要存取的目錄或檔的路徑`/`斜槓 ( ) 字元用作分隔符。
* _呼叫者憑據_指示用於訪問服務的憑據,如下所述。

訪問 Azure 數據湖儲存第 2 代時,調用方必須提供訪問服務的有效認證。 支援以下提供認證的方法:

* 將`;sharedkey=`*帳號金鑰*附加到 URI,_帳號金鑰_是儲存帳戶金鑰
* 追加`;impersonate`到URI。 Kusto 將使用請求者的主體標識並類比它來訪問資源。 主體需要具有適當的 RBAC 角色分配才能執行讀/寫操作,[此處](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)所述。 (例如,讀取操作的最小角色是`Storage Blob Data Reader`角色)。
* 將`;token=`*AadToken*追加到 URI,_其中 AadToken_是基-64 編碼的 AAD 訪問權杖(確保權杖`https://storage.azure.com/`用於資源)。
* 追加`;prompt`到URI。 Kusto 在需要訪問資源時請求使用者憑據。 (對於雲部署,將禁用提示用戶,並且僅在測試環境中啟用。
* 使用 Azure 資料儲存第 2 代的`?sig=...`標準查詢 () 提供共享存取 (SAS) 密鑰。 當 Kusto 需要存取資源有限時,請使用此方法。



### <a name="azure-data-lake-store-gen-1"></a>Azure Data Lake Store Gen 1

此提供程式支援存取 Azure 資料湖儲存中的檔案和目錄。
必須向它提供認證(Kusto 不使用自己的 AAD 主體來存取 Azure 資料湖。支援以下提供認證的方法:

* 追加`;impersonate`到URI。 Kusto 將使用請求者的主體識別並模擬它來存取資源
* 附加`;token=`_AadToken_to URI,AadToken 是基-64 編碼的 AAD 訪問權杖(確保_AadToken_`https://management.azure.com/`權杖用於資源)。
* 追加`;prompt`到URI。 Kusto 將在需要訪問資源時請求使用者憑據。 (對於雲部署,將禁用提示用戶,並且僅在測試環境中啟用。



