---
title: 機器碼中的錯誤-Azure 資料總管
description: 本文說明 Azure 資料總管中機器碼的錯誤。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: f1c076ba2c85ee18474372e4e19b285c133633d7
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013835"
---
# <a name="errors-in-native-code"></a>機器碼中的錯誤

Kusto 引擎是以機器碼撰寫，並使用負值來報告錯誤 `HRESULT` 。 這些錯誤與程式設計 API 不尋常，但可能會發生。 例如，作業失敗可能會顯示「 `Exception from HRESULT:` *HRESULT-程式碼*」狀態。

Kusto 原生錯誤碼是使用 Windows 宏搭配來定義 `MAKE-HRESULT` 的：

* 嚴重性設定為`1`
* 設施設定為`0xDA`
  
已定義下列錯誤碼。

|資訊清單常數                  |程式碼  |值       |意義                                                                                                        |
|-----------------------------------|------|------------|---------------------------------------------------------------------------------------------------------------|
|`E_EXTENT_LOAD_FAILED`             | `0`  |`0x80DA0000`|無法載入資料範圍                                                                                 |
|`E_RUNAWAY_QUERY`                  | `1`  |`0x80DA0001`|查詢執行已中止，因為它超過其允許的資源                                              |
|`E_STREAM_FORMAT`                  | `2`  |`0x80DA0002`|無法剖析資料流程，因為它的格式不正確                                                     |
|`E_QUERY_RESULT_SET_TOO_LARGE`     | `3`  |`0x80DA0003`|此查詢的結果集超過其允許的記錄/大小限制                                         |
|`E_STREAM_ENCODING_VERSION`        | `4`  |`0x80DA0004`|無法剖析結果資料流程，因為其版本不明                                                 |
|`E_KVDB_ERROR`                     | `5`  |`0x80DA0005`|內嵌索引鍵/值存放區元件中的失敗                                                              |
|`E_QUERY_CANCELLED`                | `6`  |`0x80DA0006`|查詢已取消                                                                                             |
|`E_LOW_MEMORY_CONDITION`           | `7`  |`0x80DA0007`|發生記憶體不足的狀況                                                                                  |
|`E_WRONG_NUMBER_OF_FIELDS`         | `8`  |`0x80DA0008`|輸入資料流程中的欄位數目錯誤                                                                 |
|`E_INPUT_STREAM_TOO_LARGE`         | `9`  |`0x80DA0009`|欄位/記錄/資料流程的輸入大小已超過允許的長度                                          |
|`E_ENTITY_NOT_FOUND`               | `10` |`0x80DA000A`|找不到要求的實體                                                                              |
|`E_CLOSING_QUOTE_MISSING`          | `11` |`0x80DA000B`|提交以供內嵌的 csv 資料流程有欄位包含遺漏引號                                          |
|`E_OVERFLOW`                       | `12` |`0x80DA000C`|表示算術溢位錯誤。 計算的結果對目的地類型而言太大     |
|`E_INCOMPATIBLE_TOKENIZERS`        | `13` |`0x80DA000D`|範圍合併失敗，因為合併索引的 token 化工具不相容                                    |
|`E_DYNAMIC_PROPERTY_BAG_TOO_LARGE` | `14` |`0x80DA000E`|屬性包的相異索引鍵的合併大小太大                                             |
|`E_RS_BLOCKED_ROWSTOREKEY_ERROR`   | `101`|`0x80DA0065`|嘗試存取封鎖的 RowStore 金鑰                                                           |
|`E_RS_SHUTTINGDOWN_ERROR`          | `102`|`0x80DA0066`|RowStore 正在關閉                                                                                      |
|`E_RS_LOCAL_STORAGE_FULL_ERROR`    | `103`|`0x80DA0067`|RowStore 的本機磁片儲存體已滿                                                                        |
|`E_RS_CANNOT_READ_WRITE_AHEAD_LOG` | `104`|`0x80DA0068`|從 RowStore 讀取記錄儲存體失敗                                                           |
|`E_RS_CANNOT_RETRIEVE_VALUES_ERROR`| `105`|`0x80DA0069`|無法從 RowStore 儲存體取得值                                                               |
|`E_RS_NOT_READY_ERROR`             | `106`|`0x80DA006A`|RowStore 正在初始化                                                                                       |
|`E_RS_INSERTION_THROTTLED_ERROR`   | `107`|`0x80DA006B`|已節流將值插入至 RowStore                                                                    |
|`E_RS_READONLY_ERROR`              | `108`|`0x80DA006C`|RowStore 是以唯讀狀態附加                                                                        |
|`E_RS_UNAVAILABLE_ERROR`           | `109`|`0x80DA006D`|RowStore 目前無法使用                                                                              |
|`E_RS_DOES_NOT_EXIST_ERROR`        | `110`|`0x80DA006E`|RowStore 不存在                                                                                         |
|`E_SB_QUERY_THROTTLED_ERROR`       | `200`|`0x80DA00C8`|沙箱化查詢已節流處理                                                                                  |
|`E_SB_QUERY_CANCELLED`             | `201`|`0x80DA00C9`|沙箱化查詢已取消                                                                                   |
|`E_UNSUPPORTED_INSTRUCTION_SET`    | `202`|`0x80DA00CA`|此 CPU 不支援引擎的必要指令集                                                   |
