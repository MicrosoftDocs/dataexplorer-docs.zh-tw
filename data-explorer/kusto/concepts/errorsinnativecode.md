---
title: 這個機代碼中的錯誤 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中本機代碼中的錯誤。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/12/2019
ms.openlocfilehash: fa36bec3586a5e316b08ebc2949617268f0270ab
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523251"
---
# <a name="errors-in-native-code"></a>機器碼中的錯誤

Kusto 引擎以本機代碼編寫,並使用負`HRESULT`值 報告錯誤。 這些錯誤通常不在程式設計 API 中看到,但可能發生。 例如,操作失敗可能描述`Exception from HRESULT:`*「HRESULT-CODE」* 的狀態。

Kusto 本機錯誤代碼使用`MAKE-HRESULT`Windows 宏定義, 具有:

* 嚴重性設定為`1`
* 設施設定為`0xDA`
  
定義了以下錯誤代碼:

|清單常數                  |程式碼 |值        |意義                                                                                                        |
|-----------------------------------|-----|-------------|---------------------------------------------------------------------------------------------------------------|
|`E_EXTENT_LOAD_FAILED`             | `0`  |`0x80DA0000`|無法載入資料分片                                                                                  |
|`E_RUNAWAY_QUERY`                  | `1`  |`0x80DA0001`|查詢執行在超出其允許的資源時中止                                                   |
|`E_STREAM_FORMAT`                  | `2`  |`0x80DA0002`|無法分析資料串流,因為它格式不當                                                      |
|`E_QUERY_RESULT_SET_TOO_LARGE`     | `3`  |`0x80DA0003`|此查詢的結果集超過其允許的記錄/大小限制                                            |
|`E_STREAM_ENCODING_VERSION`        | `4`  |`0x80DA0004`|無法分析結果流,因為它的版本未知                                                   |
|`E_KVDB_ERROR`                     | `5`  |`0x80DA0005`|無法執行金鑰/值資料庫操作                                                              |
|`E_QUERY_CANCELLED`                | `6`  |`0x80DA0006`|查詢已取消                                                                                            |
|`E_LOW_MEMORY_CONDITION`           | `7`  |`0x80DA0007`|由於可用程序記憶體不足,操作已中止                                              |
|`E_WRONG_NUMBER_OF_FIELDS`         | `8`  |`0x80DA0008`|提交供攝取的 csv 文件具有欄位數錯誤的記錄(相對於其他記錄)|
|`E_INPUT_STREAM_TOO_LARGE`         | `9`  |`0x80DA0009`|提交供攝取的文件已超過允許的長度                                           |
|`E_ENTITY_NOT_FOUND`               | `10` |`0x80DA000A`|找不到要求的實體                                                                             |
|`E_CLOSING_QUOTE_MISSING`          | `11` |`0x80DA000B`|提交供攝取的 csv 文件具有缺少報價的欄位                                        |
|`E_OVERFLOW`                       | `12` |`0x80DA000C`|表示算術溢出誤差(計算結果對於目標類型來說太大)    |
|`E_RS_BLOCKED_ROWSTOREKEY_ERROR`   | `101`|`0x80DA0065`|嘗試存取被封鎖的儲存金鑰                                                          |
|`E_RS_SHUTTINGDOWN_ERROR`          | `102`|`0x80DA0066`|列儲存正在關閉                                                                                     |
|`E_RS_LOCAL_STORAGE_FULL_ERROR`    | `103`|`0x80DA0067`|儲存儲存儲存的磁碟空間已滿                                                             |
|`E_RS_CANNOT_READ_WRITE_AHEAD_LOG` | `104`|`0x80DA0068`|從儲存記憶體失敗                                                                      |
|`E_RS_CANNOT_RETRIEVE_VALUES_ERROR`| `105`|`0x80DA0069`|無法從儲存記憶體儲存記憶體中。                                                              |
|`E_RS_NOT_READY_ERROR`             | `106`|`0x80DA006A`|列儲存正在初始化                                                                                      |
|`E_RS_INSERTION_THROTTLED_ERROR`   | `107`|`0x80DA006B`|限制對列儲存的值插入                                                                   |
|`E_RS_READONLY_ERROR`              | `108`|`0x80DA006C`|列儲存以唯讀狀態附加                                                                       |
|`E_RS_UNAVAILABLE_ERROR`           | `109`|`0x80DA006D`|列儲存目前無法使用                                                                             |