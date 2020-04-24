---
title: 紀錄10() - Azure數據資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 log10()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 9ccc83ff466d0414f793b7cfbbcf10d2ca169348
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513187"
---
# <a name="log10"></a>log10()

`log10()`返回公共(基-10)對數函數。  

**語法**

`log10(`*X.*`)`

**引數**

* *x*: > 0 的實數。

**傳回**

* 常見的對數是基-10對數:指數函數(exp)與基10的反向。
* `null`如果參數為負或 null 或無法`real`轉換為值。 

**另請參閱**

* 有關自然(基-e)對數,請參閱[log()](log-function.md)。
* 有關基數-2對數,請參閱日誌[2()](log2-function.md)