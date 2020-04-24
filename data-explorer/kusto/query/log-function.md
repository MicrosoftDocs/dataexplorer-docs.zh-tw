---
title: 紀錄() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 log()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 7bd3c4f5c6b262587cab2327486945f5d78aaf02
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513272"
---
# <a name="log"></a>log()

`log()`返回自然對數函數。  

**語法**

`log(`*X.*`)`

**引數**

* *x*: > 0 的實數。

**傳回**

* 自然對數是基-e對數:自然指數函數(exp)的逆向。
* `null`如果參數為負或 null 或無法`real`轉換為值。 

**另請參閱**

* 有關公共(基-10)對數,請參閱[log10()](log10-function.md)。
* 有關基數-2對數,請參閱日誌[2()](log2-function.md)