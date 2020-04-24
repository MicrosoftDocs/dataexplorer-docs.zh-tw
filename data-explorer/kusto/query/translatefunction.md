---
title: 翻譯() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的翻譯()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: f128ce31af7fc720ee81b7471d3d74616197b8d4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505707"
---
# <a name="translate"></a>translate()

將一組字元("搜索清單")替換為給定字串中的另一組字元("替換清單")。
此函式搜尋「搜尋清單」的字元,並將其取代為「取代清單」中的字元

**語法**

`translate(`*搜尋清單*`,`*取代清單*`,`*文字*`)`

**引數**

* *搜尋清單*:要取代的字元清單
* *取代清單*:要取代「搜尋清單」的字元清單
* *文字*: 要搜尋的字串

**傳回**

將「替代清單」 中字元的所有貨幣取代為「搜尋清單」 的字元後*的文字*

**範例**

|輸入                                 |輸出   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|