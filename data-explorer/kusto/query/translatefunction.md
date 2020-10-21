---
title: '轉譯 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明如何在 Azure 資料總管中轉譯 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: 5d186172a0be1780347dbf89600c200c00687291
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252035"
---
# <a name="translate"></a>translate()

以指定字串中 ( ' replacementList ' ) 的另一組字元來取代一組字元 ( ' searchList ' ) 。
函數會搜尋 ' searchList ' 中的字元，並以 ' replacementList ' 中的對應字元取代它們

## <a name="syntax"></a>語法

`translate(`*searchList* `,`*replacementList* `,`*文字*`)`

## <a name="arguments"></a>引數

* *searchList*：應取代的字元清單
* *replacementList*：應取代 ' searchList ' 中之字元的字元清單
* *text*：要搜尋的字串

## <a name="returns"></a>傳回

以 ' searchList ' 中的對應字元取代 ' replacementList ' 中的所有字元 ocurrences 之後的*文字*

## <a name="examples"></a>範例

|輸入                                 |輸出   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|