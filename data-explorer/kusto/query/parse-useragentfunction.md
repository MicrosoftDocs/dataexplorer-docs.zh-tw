---
title: parse_user_agent （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 parse_user_agent （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 4b4371ee691cea65096cff683a348fcac31e7e48
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346400"
---
# <a name="parse_user_agent"></a>parse_user_agent()

解釋使用者代理字串，其可識別使用者的瀏覽器，並提供特定系統詳細資料給裝載使用者所造訪網站的伺服器。 結果會以的形式傳回 [`dynamic`](./scalar-data-types/dynamic.md) 。 

## <a name="syntax"></a>語法

`parse_user_agent(`*使用者-代理程式-字串*，*尋找*`)`

## <a name="arguments"></a>引數

* *使用者代理字串*：類型的運算式 `string` ，代表使用者代理字串。

* *尋找*：類型為或的運算式 `string` `dynamic` ，表示函式應該在使用者代理字串（剖析目標）中尋找的內容。 可能的選項：「瀏覽器」、「os」、「裝置」。 如果只需要單一剖析目標，可以將參數傳遞給它 `string` 。
如果需要兩個或三個，則可以將它們當做傳遞 `dynamic array` 。

## <a name="returns"></a>傳回

類型的物件 `dynamic` ，其中包含所要求剖析目標的相關資訊。

瀏覽器：家族、MajorVersion、MinorVersion、Patch                 

作業系統：系列、MajorVersion、MinorVersion、Patch、PatchMinor             

裝置：系列、品牌、型號

> [!WARNING]
> 函式會根據輸入字串的 RegEx 檢查，針對大量預先定義的模式建立。 因此，預期的時間和 CPU 耗用量很高。
在查詢中使用函式時，請確定它以分散式方式在多部電腦上執行。
如果經常使用此函式的查詢，您可能會想要透過[更新原則](../management/updatepolicy.md)預先建立結果，但您必須考慮在更新原則中使用此函式將會增加內嵌延遲。
 
## <a name="example"></a>範例

```kusto
print useragent = "Mozilla/5.0 (Windows; U; en-US) AppleWebKit/531.9 (KHTML, like Gecko) AdobeAIR/2.5.1"
| extend x = parse_user_agent(useragent, "browser") 
```

預期的結果是動態物件：

{"Browser"： {"家族"： "AdobeAIR"，"MajorVersion"： "2"，"MinorVersion"： "5"，"Patch"： "1"}}

```kusto
print useragent = "Mozilla/5.0 (SymbianOS/9.2; U; Series60/3.1 NokiaN81-3/10.0.032 Profile/MIDP-2.0 Configuration/CLDC-1.1 ) AppleWebKit/413 (KHTML, like Gecko) Safari/4"
| extend x = parse_user_agent(useragent, dynamic(["browser","os","device"])) 
```

預期的結果是動態物件：

{"Browser"： {"家族"： "Nokia OSS Browser"，"MajorVersion"： "3"，"MinorVersion"： "1"，"Patch"： ""}，"作業系統"： {"家族"： "Symbian OS"，"MajorVersion"： "9"，"MinorVersion"： "2"，"Patch"： ""，"PatchMinor"： ""}，"裝置"： {"家族"： "Nokia N81"，"品牌"： "Nokia"，"Model"： "N81-3"}}