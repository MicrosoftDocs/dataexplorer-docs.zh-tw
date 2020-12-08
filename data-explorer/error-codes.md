---
title: Azure 資料總管中的內嵌錯誤代碼
description: 本主題列出 Azure 資料總管中的內嵌錯誤代碼
author: orspod
ms.author: orspodek
ms.reviewer: vladikbr
ms.service: data-explorer
ms.topic: reference
ms.date: 11/11/2020
ms.openlocfilehash: aeef0c9295fbb22c225068fb240670fb7d637a98
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96776562"
---
# <a name="ingestion-error-codes-in-azure-data-explorer"></a>Azure 資料總管中的內嵌錯誤代碼

下列清單包含 [您在內嵌期間可能](ingest-data-overview.md)會遇到的錯誤碼。 當您在叢集上啟用失敗的內嵌 [診斷記錄](using-diagnostic-logs.md#ingestion-logs-schema) 時，您可以在 **失敗** 的內嵌作業記錄檔中看到錯誤碼。 您也可以監視內嵌 **結果** 計量 [，以查看](using-metrics.md#ingestion-metrics) 內嵌錯誤的 **類別** ，而不是特定的錯誤碼。 下列錯誤會依這類分類進行組織。 

> [!NOTE]
> 如果錯誤是暫時性的，則重試內嵌可能會成功。

## <a name="category-badformat"></a>Category： BadFormat

|錯誤訊息                                 |描述                                           |永久/暫時性|
|---|---|---|
|Stream_WrongNumberOfFields                        |輸入記錄中的欄位數目不一致。 HRESULT：0x80DA0008      |持續性           |
|Stream_ClosingQuoteMissing                        |CSV 格式無效。 遺漏右引號。 HRESULT：0x80DA000b            |持續性           |
|BadRequest_InvalidBlob                            |Blob 無效。                                                              |持續性           |
|BadRequest_EmptyArchive                           |封存是空的。                                                             |持續性           |
|BadRequest_InvalidArchive                         |封存無效。                                                           |持續性           |
|BadRequest_InvalidMapping                         |無法剖析內嵌對應。<br>如需如何撰寫內嵌對應的詳細資訊，請參閱 [資料](./kusto/management/mappings.md)對應。   |持續性           |
|BadRequest_InvalidMappingReference                |不正確對應參考。            |持續性           |
|BadRequest_FormatNotSupported                     |不支援格式。 這可能是因為您使用的是特定資料連線不支援的格式。 <br>如需 Azure 資料總管進行內嵌所支援資料格式的詳細資訊，請參閱 [支援的資料格式](ingestion-supported-formats.md)。 |持續性          |
|BadRequest_InconsistentMapping                    |支援的內嵌對應與現有的資料表架構不一致。 |持續性           |
|BadRequest_UnexpectedCharacterInInputStream       |輸入資料流程中有未預期的字元。                                     |持續性           |

## <a name="category-badrequest"></a>Category： BadRequest

|錯誤訊息                                 |描述                                           |永久/暫時性|
|---|---|---|
|BadRequest_EmptyBlob                              |Blob 是空的。                                                               |持續性           |
|BadRequest_EmptyBlobUri                           |Blob Uri 是空的。                                                           |持續性           |
|BadRequest_DuplicateMapping                       |內嵌屬性包含 ingestionMapping 和 ingestionMappingReference，但無效。              |持續性          |
|BadRequest_InvalidOrEmptyTableName                |資料表名稱空白或無效。<br>如需 Azure 資料總管命名慣例的詳細資訊，請參閱 [機構名稱](./kusto/query/schema-entities/entity-names.md)。    |持續性          |
|BadRequest_EmptyDatabaseName                      |資料庫名稱是空的。             |持續性          |
|BadRequest_EmptyMappingReference                  |某些格式應該會取得內嵌對應以進行內嵌，而且對應參考是空的。<br>如需對應的詳細資訊，請參閱 [資料對應](./kusto/management/mappings.md)。        |持續性           |
|Download_BadRequest                               |因為要求不正確，所以無法從 Azure 儲存體下載來源。    |持續性           |
|BadRequest_MissingMappingtFailure                 |Avro 和 Json 格式必須使用 ingestionMapping 或 ingestionMappingReference 參數內嵌。         |持續性           |
|Stream_NoDataToIngest                             |找不到要內嵌的資料。<br>針對 JSON 格式的資料，此錯誤可能表示 JSON 格式無效。        |持續性          |
|Stream_DynamicPropertyBagTooLarge                 |資料在動態資料行中包含太大的值。 HRESULT：0x80DA000E         |持續性          |
|General_BadRequest                                |不正確的要求。            |持續性           |
|BadRequest_CorruptedMessage                       |訊息已損毀。    |持續性           |
|BadRequest_SyntaxError                            |要求語法錯誤。     |持續性           |
|BadRequest_ZeroRetentionPolicyWithNoUpdatePolicy  |資料表的保留原則為零，而且不是任何更新原則的來源資料表。    |持續性           |
|BadRequest_CreationTimeEarlierThanSoftDeletePeriod|針對內嵌指定的建立時間不在中 `SoftDeletePeriod` 。<br>如需的詳細資訊 `SoftDeletePeriod` ，請參閱 [原則物件](./kusto/management/retentionpolicy.md#the-policy-object)。  |持續性   |
|BadRequest_NotSupported                           |不支援要求。    |持續性           |
|Download_SourceNotFound                           |無法從 Azure 儲存體下載來源。 找不到來源。       |持續性       |
|BadRequest_EntityNameIsNotValid                   |機構名稱無效。<br>如需 Azure 資料總管命名慣例的詳細資訊，請參閱 [機構名稱](./kusto/query/schema-entities/entity-names.md)。    |持續性           |
|BadRequest_MalformedIngestionProperty              |內嵌屬性的格式不正確。    |持續性           |
| BadRequest_IngestionPropertyNotSupportedInThisCoNtext | 此內容中不支援內嵌屬性| 持續性

## <a name="category-dataaccessnotauthorized"></a>Category： DataAccessNotAuthorized

|錯誤訊息                                 |描述                                           |永久/暫時性|
|---|---|---|
|Download_AccessConditionNotSatisfied              |無法從 Azure 儲存體下載來源。 未滿足存取條件。     |持續性           |
|Download_Forbidden                                |無法從 Azure 儲存體下載來源。 禁止存取。    |持續性           |
|Download_AccountNotFound                          |無法從 Azure 儲存體下載來源。 找不到帳戶。    |持續性           |
|BadRequest_TableAccessDenied                      |拒絕存取資料表。<br>如需詳細資訊，請參閱 [Kusto 中的以角色為基礎的授權](./kusto/management/access-control/role-based-authorization.md)。     |持續性           |
|BadRequest_DatabaseAccessDenied                   |拒絕存取資料庫。<br>如需詳細資訊，請參閱 [Kusto 中的以角色為基礎的授權](./kusto/management/access-control/role-based-authorization.md)。                        |持續性           |

## <a name="category-downloadfailed"></a>Category： >downloadfailed

|錯誤訊息                                 |描述                                           |永久/暫時性|
|---|---|---|
|Download_NotTransient                             |無法從 Azure 儲存體下載來源。 未發生暫時性錯誤                   |持續性           |
|Download_UnknownError                             |無法從 Azure 儲存體下載來源。 發生未知錯誤              |暫時性           |


## <a name="category-entitynotfound"></a>Category： EntityNotFound

|錯誤訊息                                 |描述                                           |永久/暫時性|
|---|---|---|
|BadRequest_MappingReferenceWasNotFound            |找不到對應參考。   |持續性           |
|BadRequest_DatabaseNotExist                       |資料庫不存在。          |持續性           |
|BadRequest_TableNotExist                          |資料表不存在。          |持續性           |
|BadRequest_EntityNotFound                         |找不到 Azure 資料總管實體 (，例如找不到對應、資料庫或資料表) 。           |持續性           |

## <a name="category-filetoolarge"></a>Category： FileTooLarge

|錯誤訊息                                 |描述                                           |永久/暫時性|
|---|---|---|
|Stream_InputStreamTooLarge                        |輸入資料的大小總計或資料中的單一欄位太大。 HRESULT：0x80DA0009                 |持續性          |
|BadRequest_FileTooLarge                           |Blob 大小已超過內嵌所允許的大小限制。<br>如需有關內嵌大小限制的詳細資訊，請參閱 [Azure 資料總管資料內嵌總覽](ingest-data-overview.md#comparing-ingestion-methods-and-tools)。 |持續性           |

## <a name="category-internalserviceerror"></a>Category： InternalServiceError

|錯誤訊息                                 |描述                                           |永久/暫時性|
|---|---|---|
|General_InternalServerError                       |發生內部伺服器錯誤。                     |暫時性          |
|General_TransientSchemaMismatch                   |啟動內嵌時的目標資料表架構，與認可內嵌時的架構不相符。         |暫時性           |
|逾時                                            |作業已中止，因為有超時時間。     |暫時性           |
|BadRequest_MessageExhausted                       |因為內嵌已達到重試次數上限或重試期間上限，所以無法內嵌資料。<br>重試內嵌可能會成功。   |暫時性          |

## <a name="category-updatepolicyfailure"></a>Category： UpdatePolicyFailure

|錯誤訊息                                 |描述                                           |永久/暫時性|
|---|---|---|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema   |無法叫用更新原則。 查詢架構與資料表架構不符。     |持續性           |
|UpdatePolicy_FailedDescendantTransaction          |無法叫用更新原則。 失敗的子交易更新原則。    |暫時性           |
|UpdatePolicy_IngestionError                       |無法叫用更新原則。 發生內嵌錯誤。<br>此錯誤會在更新原則的來源資料表上報告。     |暫時性          |
|UpdatePolicy_UnknownError                         |無法叫用更新原則。 發生未知錯誤。<br>此錯誤會在更新原則的目標資料表上報告。    |暫時性           |
|UpdatePolicy_Cyclic_Update_Not_Allowed         |無法叫用更新原則。 不允許迴圈更新。      |持續性           |

## <a name="category-useraccessnotauthorized"></a>Category： UserAccessNotAuthorized

|錯誤訊息                                 |描述                                           |永久/暫時性|
|---|---|---|
|BadRequest_InvalidKustoIdentityToken              |Kusto 識別權杖無效。                           |持續性           |
|禁止                                          |安全性許可權不足，無法執行要求。 | 持續性|

## <a name="category-unknown"></a>類別：未知

|錯誤訊息                                 |描述                                           |永久/暫時性|
|---|---|---|
|Unknown                                            |發生未知錯誤。                             |暫時性          |
