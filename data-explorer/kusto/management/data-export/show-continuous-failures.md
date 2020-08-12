---
title: 顯示連續資料匯出失敗-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中顯示連續資料匯出失敗。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 54c95e3beecd25b504c52c2fe06ffa51160ac8ca
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149415"
---
# <a name="show-continuous-export-failures"></a>顯示連續匯出失敗

傳回連續匯出過程中記錄的所有失敗。 依據命令中的 Timestamp 資料行來篩選結果，只查看感興趣的時間範圍。 

## <a name="syntax"></a>語法

`.show``continuous-export` *ContinuousExportName*`failures`

## <a name="properties"></a>屬性

| 屬性             | 類型   | 描述                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連續匯出的名稱  |

## <a name="output"></a>輸出

| 輸出參數 | 類型      | 描述                                         |
|------------------|-----------|-----------------------------------------------------|
| 時間戳記        | Datetime  | 失敗的時間戳記。                           |
| OperationId      | String    | 失敗的作業識別碼。                    |
| 名稱             | String    | 連續匯出名稱。                             |
| LastSuccessRun   | 時間戳記 | 最後一次成功執行連續匯出。   |
| FailureKind      | String    | 失敗/PartialFailure。 PartialFailure 表示某些成品在失敗發生之前已成功匯出。 |
| 詳細資料          | String    | 失敗錯誤詳細資料。                              |

## <a name="example"></a>範例 

```kusto
.show continuous-export MyExport failures 
```

| 時間戳記                   | OperationId                          | 名稱     | LastSuccessRun              | FailureKind | 詳細資料    |
|-----------------------------|--------------------------------------|----------|-----------------------------|-------------|------------|
| 2019-01-01 11：07：41.1887304 | ec641435-2505-4532-ba19-d6ab88c96a9d | MyExport | 2019-01-01 11：06：35.6308140 | 失敗     | 詳細資料 .。。 |
