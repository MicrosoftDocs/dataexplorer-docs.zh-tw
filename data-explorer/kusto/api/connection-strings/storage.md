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
ms.openlocfilehash: 0456ad7115c51bcdc51b0db82bc9f9b88953be32
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226205"
---
# <a name="storage-connection-strings"></a>儲存體連接字串

幾個 Kusto 命令會指示 Kusto 與外部儲存體服務互動。 例如，您可以告訴 Kusto 將資料匯出至 Azure 儲存體 Blob，在此情況下，必須提供特定的參數（例如儲存體帳戶名稱或 Blob 容器）。

Kusto 支援下列存放裝置提供者：


* Azure 儲存體 Blob 存放裝置提供者
* Azure Data Lake Storage 存放裝置提供者

每種存放裝置提供者都會定義用來描述存放裝置資源的連接字串格式，以及如何存取它們。
Kusto 會使用 URI 格式來描述這些儲存體資源，以及存取它們所需的屬性（例如安全性認證）。


|提供者                   |配置    |URI 範本                          |
|---------------------------|----------|--------------------------------------|
|Azure 儲存體 Blob         |`https://`|`https://`*帳戶* `.blob.core.windows.net/`*容器*[ `/` *BlobName*] [ `?` *SasKey* \| `;` *AccountKey*]|
|Azure Data Lake Store Gen 2|`abfss://`|`abfss://`*檔案系統* `@`*帳戶* `.dfs.core.windows.net/`*PathToDirectoryOrFile*[ `;` *CallerCredentials*]|
|Azure Data Lake Store Gen 1|`adl://`  |`adl://`* *Azuredatalakestore.net/*PathToDirectoryOrFile*[ `;` *CallerCredentials*]|

## <a name="azure-storage-blob"></a>Azure 儲存體 Blob

此提供者是最常使用的，而且在所有案例中都支援。
存取資源時，必須指定提供者的認證。 提供認證的支援機制有兩種：

* 使用 Azure 儲存體 Blob 的標準查詢（）提供共用存取（SAS）金鑰 `?sig=...` 。 當 Kusto 需要在有限時間存取資源時，請使用這個方法。
* 提供儲存體帳戶金鑰（ `;ljkAkl...==` ）。 當 Kusto 需要持續存取資源時，請使用這個方法。

範例（請注意，這會顯示模糊的字串常值，因此不會公開帳戶金鑰或 SAS）：

`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv;<storage_account_key_text, ends with '=='>"`
`h"https://fabrikam.blob.core.windows.net/container/path/to/file.csv?sv=...&sp=rwd"` 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="azure-data-lake-store-gen-2"></a>Azure Data Lake Store Gen 2

此提供者支援存取 Azure Data Lake 存放區 Gen 2 中的資料。

URI 的格式為：

`abfss://`*檔案系統* `@`*StorageAccountName* `.dfs.core.windows.net/`*路徑* `;`*CallerCredentials*

其中：

* _Filesystem_是 ADLS filesystem 物件的名稱（大致上等同于 Blob 容器）
* _StorageAccountName_是儲存體帳戶的名稱
* _Path_是要存取之目錄或檔案的路徑， `/` 並使用斜線（）字元做為分隔符號。
* _CallerCredentials_表示用來存取服務的認證，如下所述。

存取 Azure Data Lake 存放區 Gen 2 時，呼叫端必須提供有效的認證來存取服務。 支援下列提供認證的方法：

* 將 `;sharedkey=` *AccountKey*附加至 URI，並將_AccountKey_設為儲存體帳戶金鑰
* 將附加 `;impersonate` 至 URI。 Kusto 會使用要求者的主體身分識別，並模擬它來存取資源。 主體必須具有適當的 RBAC 角色指派，才能執行讀取/寫入作業，如[這裡](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)所述。 （例如，讀取作業的最小角色是 `Storage Blob Data Reader` 角色）。
* 將 `;token=` *AadToken*附加至 URI， _AadToken_是以64編碼的 AAD 存取權杖（請確定該資源的權杖是 `https://storage.azure.com/` ）。
* 將附加 `;prompt` 至 URI。 Kusto 會在需要存取資源時要求使用者認證。 （提示使用者已停用雲端部署，而且只會在測試環境中啟用）。
* 使用 Azure Data Lake Storage Gen 2 的標準查詢（）提供共用存取（SAS）金鑰 `?sig=...` 。 當 Kusto 需要在有限時間存取資源時，請使用這個方法。



### <a name="azure-data-lake-store-gen-1"></a>Azure Data Lake Store Gen 1

此提供者支援存取 Azure Data Lake 存放區中的檔案和目錄。
必須以認證提供（Kusto 不會使用自己的 AAD 主體來存取 Azure Data Lake）。支援下列提供認證的方法：

* 將附加 `;impersonate` 至 URI。 Kusto 會使用要求者的主體身分識別，並模擬它來存取資源
* 將 `;token=` *AadToken*附加至 URI， *AadToken*是以64編碼的 AAD 存取權杖（請確定該資源的權杖是 `https://management.azure.com/` ）。
* 將附加 `;prompt` 至 URI。 Kusto 會在需要存取資源時要求使用者認證。 （提示使用者已停用雲端部署，而且只會在測試環境中啟用）。



