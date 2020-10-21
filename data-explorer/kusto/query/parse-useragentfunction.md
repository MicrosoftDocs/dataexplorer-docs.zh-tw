---
title: 'parse_user_agent ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 parse_user_agent。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: a7261f6996aecdf10446c990dac54afa4638147e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246309"
---
# <a name="parse_user_agent"></a>parse_user_agent()

解釋使用者代理字串，該字串可識別使用者的瀏覽器，並提供特定的系統詳細資料給裝載使用者所造訪網站的伺服器。 結果會傳回為 [`dynamic`](./scalar-data-types/dynamic.md) 。 

## <a name="syntax"></a>語法

`parse_user_agent(`*使用者代理程式-字串*、 *搜尋*`)`

## <a name="arguments"></a>引數

* *使用者代理程式-字串*：類型的運算式 `string` ，代表使用者代理字串。

* *查詢*：類型為或的運算式 `string` `dynamic` ，表示函式在使用者代理程式字串中要尋找的內容 (剖析目標) 。 可能的選項： "browser"、"os"、"device"。 如果只需要單一剖析目標，則可以將參數傳遞給它 `string` 。
如果需要兩個或三個，它們可以作為傳遞 `dynamic array` 。

## <a name="returns"></a>傳回

型別的物件 `dynamic` ，包含所要求剖析目標的相關資訊。

Browser： Family、MajorVersion、MinorVersion、Patch                 

作業系統：系列、MajorVersion、MinorVersion、Patch、PatchMinor             

裝置：系列、品牌、型號

> [!WARNING]
> 函式的實作為針對大量預先定義模式的輸入字串 RegEx 檢查。 因此，預期的時間和 CPU 耗用量很高。
當函數用於查詢時，請確定它是在多部電腦上以分散式方式執行。
如果經常使用此函式的查詢，您可能會想要透過 [更新原則](../management/updatepolicy.md)預先建立結果，但您必須考慮在更新原則中使用此函式會增加內嵌延遲。
 
## <a name="example"></a>範例

```kusto
print useragent = "Mozilla/5.0 (Windows; U; en-US) AppleWebKit/531.9 (KHTML, like Gecko) AdobeAIR/2.5.1"
| extend x = parse_user_agent(useragent, "browser") 
```

預期的結果是動態物件：

{"Browser"： {"系列"： "AdobeAIR"，"MajorVersion"： "2"，"MinorVersion"： "5"，"Patch"： "1"}}

```kusto
print useragent = "Mozilla/5.0 (SymbianOS/9.2; U; Series60/3.1 NokiaN81-3/10.0.032 Profile/MIDP-2.0 Configuration/CLDC-1.1 ) AppleWebKit/413 (KHTML, like Gecko) Safari/4"
| extend x = parse_user_agent(useragent, dynamic(["browser","os","device"])) 
```

預期的結果是動態物件：

{"Browser"： {"系列"： "Nokia OSS Browser"，"MajorVersion"： "3"，"MinorVersion"： "1"，"Patch"： ""}，"作業系統"： {"系列"： "Symbian OS"，"MajorVersion"： "9"，"MinorVersion"： "2"，"Patch"： ""，"PatchMinor"： ""}，"Device"： {"系列"： "Nokia N81"，"品牌"： "Nokia"，"Model"： "N81-3"}}