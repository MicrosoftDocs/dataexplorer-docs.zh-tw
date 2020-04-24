---
title: parse_user_agent() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的parse_user_agent()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 2ebe85c622dda116366e30ba22e2e495e4cb76ac
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511725"
---
# <a name="parse_user_agent"></a>parse_user_agent()

解釋使用者代理字串,該字串標識用戶的瀏覽器,並為承載使用者訪問的網站的伺服器提供某些系統詳細資訊。 結果傳回[`dynamic`](./scalar-data-types/dynamic.md)為 。 

**語法**

`parse_user_agent(`*使用者代理字串*,*尋找*`)`

**引數**

* *使用者代理字串*:表示`string`使用者代理字串的類型運算式。

* *尋找*:`string`類型`dynamic`或的表示式,表示函數應在使用者代理字串中尋找的內容(解析目標)。 可能的選項:「瀏覽器"、"os"、"設備"。 如果只需要一個分析目標,則可以傳遞參數`string`。
如果需要兩個或三個,它們可以作為 傳遞`dynamic array`作為 。

**傳回**

`dynamic`包含有關請求的分析目標的資訊類型物件。

瀏覽器: 家庭, 主要版本, 次要版本, 補丁                 

操作系統: 家庭, 主要版本, 次要版本, 補丁, 補丁             

裝置: 家庭, 品牌, 型號

> [!WARNING]
> 函數實現基於對大量預定義模式的輸入字串的 regex 檢查建構。 因此,預期時間和 CPU 消耗量較高。
當在查詢中使用函數時,請確保它在多台計算機上以分散式方式運行。
如果經常使用具有此函數的查詢,則可能需要通過[更新策略](../management/updatepolicy.md)預創建結果,但需要考慮在更新策略中使用此函數會增加引入延遲。
 
**範例**

```kusto
print useragent = "Mozilla/5.0 (Windows; U; en-US) AppleWebKit/531.9 (KHTML, like Gecko) AdobeAIR/2.5.1"
| extend x = parse_user_agent(useragent, "browser") 
```

預期結果是動態物件:

• "瀏覽器":\"家庭":"AdobeAIR","主要版本":"2","次要版本":"5","補丁":"1"|

```kusto
print useragent = "Mozilla/5.0 (SymbianOS/9.2; U; Series60/3.1 NokiaN81-3/10.0.032 Profile/MIDP-2.0 Configuration/CLDC-1.1 ) AppleWebKit/413 (KHTML, like Gecko) Safari/4"
| extend x = parse_user_agent(useragent, dynamic(["browser","os","device"])) 
```

預期結果是動態物件:

• "瀏覽器":\"家庭":"諾基亞 OSS 瀏覽器","主要版本":"3", 次要版本":"1","補丁":"","操作系統":"家庭":"Symbian OS","主要版本":"9","次要版本":"2","補丁","補丁":"\","設備":""家庭":"諾基亞N81","品牌":"諾基亞","模型","#"#"#","系列":"諾基亞N81","品牌":""諾基亞","諾基亞 ","模型":"諾基亞","型號":"諾基亞","型號":"##"""補丁","補丁","設備":""家庭":"諾基亞N81","品牌":"諾基亞","型號","諾基亞","模型":"N81","#"#"+"+""""設備":"諾基亞N81","品牌":"""諾基亞","型號":"諾基亞"