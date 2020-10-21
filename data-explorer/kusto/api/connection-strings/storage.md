---
title: 儲存體連接字串-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的儲存體連接字串。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 4b212435eb506ce71b52e19d3304461e9be35b5e
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342496"
---
# <a name="storage-connection-strings"></a>儲存體連接字串

一些 Kusto 命令會指示 Kusto 與外部儲存體服務互動。 例如，您可以告知 Kusto 將資料匯出至 Azure 儲存體 Blob，在此情況下，必須提供特定參數 (例如儲存體帳戶名稱或 Blob 容器) 。

Kusto 支援下列儲存提供者：


* Azure 儲存體 Blob 存放裝置提供者
* Azure Data Lake Storage 存放裝置提供者

每種存放裝置提供者都會定義用來描述儲存體資源的連接字串格式，以及如何存取這些資源。
Kusto 會使用 URI 格式來描述這些儲存體資源，以及存取它們所需的屬性 (例如) 的安全性認證。


|提供者                   |配置    |URI 範本                          |
|---------------------------|----------|--------------------------------------|
|Azure 儲存體 Blob         |`https://`|`https://`*帳戶* `.blob.core.windows.net/`*容器*[ `/` *BlobName*] [ `?` *SasKey* \| `;` *AccountKey*]|
|Azure Data Lake Store Gen 2|`abfss://`|`abfss://`*檔案系統* `@`*帳戶* `.dfs.core.windows.net/`*PathToDirectoryOrFile*[ `;` *CallerCredentials*]|
|Azure Data Lake Store Gen 1|`adl://`  |`adl://`*Account*. azuredatalakestore.net/*PathToDirectoryOrFile*[ `;` *CallerCredentials*]|

## <a name="azure-storage-blob"></a>Azure 儲存體 Blob

這是最常使用的提供者，而且在所有案例中都支援。
存取資源時，必須獲得提供者的認證。 有兩種支援的機制可提供認證：

* 使用 Azure 儲存體 Blob 的標準查詢 () ，提供共用存取 (SAS) 金鑰 `?sig=...` 。 當 Kusto 需要在有限的時間存取資源時，請使用此方法。
*  () 提供儲存體帳戶金鑰 `;ljkAkl...==` 。 當 Kusto 需要持續存取資源時，請使用此方法。

範例 (請注意，這會顯示模糊的字串常值，因此不會公開帳戶金鑰或 SAS) ：

`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv;<storage_account_key_text, ends with '=='>"`
`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv?sv=...&sp=rwd"` 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="azure-data-lake-store-gen-2"></a>Azure Data Lake Store Gen 2

此提供者支援存取 Azure Data Lake 存放區 Gen 2 中的資料。

URI 的格式為：

`abfss://`*檔案系統* `@`*StorageAccountName* `.dfs.core.windows.net/`*路徑* `;`*CallerCredentials*

其中：

* _Filesystem_ 是 ADLS 檔案系統物件的名稱， (大致等同于 Blob 容器) 
* _StorageAccountName_ 是儲存體帳戶的名稱
* _路徑_ 是用來存取的目錄或檔案的路徑， (`/`) 字元作為分隔符號。
* _CallerCredentials_ 表示用來存取服務的認證，如下所述。

存取 Azure Data Lake 存放區 Gen 2 時，呼叫端必須提供有效的認證以存取服務。 支援下列提供認證的方法：

* 將 `;sharedkey=` *AccountKey*附加至 URI， _AccountKey_是儲存體帳戶金鑰
* 附加 `;impersonate` 至 URI。 Kusto 會使用要求者的主體身分識別，並模擬它以存取資源。 主體必須具有適當的 RBAC 角色指派，才能執行讀取/寫入作業，如 [這裡](/azure/storage/blobs/data-lake-storage-access-control)所述。  (例如，讀取作業的最基本角色是 `Storage Blob Data Reader`) 角色。
* 將 `;token=` *AadToken*附加至 URI， _AadToken_是以 BASE-64 編碼的 AAD 存取權杖 (確定資源) 的權杖 `https://storage.azure.com/` 。
* 附加 `;prompt` 至 URI。 Kusto 需要存取資源時，會要求使用者認證。  (提示使用者已針對雲端部署停用，且只在測試環境中啟用。 ) 
* 使用 Azure Data Lake Storage Gen 2 的標準查詢 () ，提供共用存取 (SAS) 金鑰 `?sig=...` 。 當 Kusto 需要在有限的時間存取資源時，請使用此方法。



### <a name="azure-data-lake-store-gen-1"></a>Azure Data Lake Store Gen 1

此提供者支援存取 Azure Data Lake 存放區中的檔案和目錄。
您必須提供認證， (Kusto 不會使用自己的 AAD 主體來存取 Azure Data Lake。 ) 支援下列提供認證的方法：

* 附加 `;impersonate` 至 URI。 Kusto 會使用要求者的主體身分識別，並模擬它來存取資源
* 將 `;token=` *AadToken*附加至 URI， *AadToken*是以 BASE-64 編碼的 AAD 存取權杖 (確定資源) 的權杖 `https://management.azure.com/` 。
* 附加 `;prompt` 至 URI。 Kusto 會在需要存取資源時要求使用者認證。  (提示使用者已針對雲端部署停用，且只在測試環境中啟用。 ) 