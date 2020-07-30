---
title: 翻譯（）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的轉譯（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: d0e99048f3f1b0e3ce5c6c59a65ea645b22d15fe
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340049"
---
# <a name="translate"></a>translate()

在指定的字串中，以另一組字元（' replacementList '）取代一組字元（' searchList '）。
函式會搜尋 ' searchList ' 中的字元，並將其取代為 ' replacementList ' 中的對應字元

## <a name="syntax"></a>語法

`translate(`*searchList* `,`*replacementList* `,`*文字*`)`

## <a name="arguments"></a>引數

* *searchList*：應取代的字元清單
* *replacementList*：應取代 ' searchList ' 中之字元的字元清單
* *text*：要搜尋的字串

## <a name="returns"></a>傳回

將 ' replacementList ' 中所有字元的 ocurrences 取代為 ' searchList ' 中的對應字元之後的*文字*

## <a name="examples"></a>範例

|輸入                                 |輸出   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|