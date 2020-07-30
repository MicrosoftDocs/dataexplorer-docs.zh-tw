---
title: infer_storage_schema 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 infer_storage_schema 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: f5ad4cdc2b74ddb62a4572249bb06fab6c656243
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347420"
---
# <a name="infer_storage_schema-plugin"></a>infer_storage_schema 外掛程式

此外掛程式會推斷外部資料的架構，並將其傳回為 CSL 架構字串。 [建立外部資料表](../management/external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table)時，可以使用此字串。

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
  ],
  'DataFormat': 'parquet',
  'FileExtension': '.parquet'
});
evaluate infer_storage_schema(options)
```

## <a name="syntax"></a>語法

`evaluate` `infer_storage_schema(` *選項。* `)`

## <a name="arguments"></a>引數

單一*選項*引數是類型的常數值 `dynamic` ，其中包含指定要求屬性的屬性包：

|名稱                    |必要|描述|
|------------------------|--------|-----------|
|`StorageContainers`|是|[儲存體連接字串](../api/connection-strings/storage.md)的清單，代表已儲存資料成品的前置詞 URI|
|`DataFormat`|是|其中一種支援的[資料格式](../../ingestion-supported-formats.md)。|
|`FileExtension`|否|僅掃描以這個副檔名結尾的檔案。 這不是必要的，但指定它可能會加速處理常式（或排除資料讀取問題）|
|`FileNamePrefix`|否|僅掃描開頭為此前置詞的檔案。 這不是必要的，但指定它可能會加速進程|
|`Mode`|否|架構推斷策略，下列其中一個： `any` 、 `last` 、 `all` 。 從任何（第一個找到的）檔案、最後寫入的檔案，或從所有檔案推斷資料架構。 預設值是 `last`。|

## <a name="returns"></a>傳回

外掛程式會傳回 `infer_storage_schema` 單一結果資料表，其中包含保留了 CSL 架構字串的單一資料列/資料行。

> [!NOTE]
> * 儲存體容器 URI 秘密金鑰除了*讀取*之外，還必須具有*清單*的許可權。
> * 架構推斷策略「全部」是一種非常「昂貴」的作業，因為它意味著從找到的*所有*成品讀取併合並其架構。
> * 某些傳回的類型可能不是由錯誤的類型猜測所造成的實際值（或做為架構合併程式的結果）。 這就是為什麼您應該先仔細檢查結果，再建立外部資料表。

## <a name="example"></a>範例

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/MovileEvents/2015;secretKey'
  ],
  'FileExtension': '.parquet',
  'FileNamePrefix': 'part-',
  'DataFormat': 'parquet'
});
evaluate infer_storage_schema(options)
```

*結果*

|CslSchema|
|---|
|app_id： string、user_id： long、event_time： datetime、country： string、city： string、device_type： string、device_vendor： string、ad_network： string、行銷： string、site_id： string、event_type： string、event_name： string、有機： string、days_from_install： int、收益： real|
