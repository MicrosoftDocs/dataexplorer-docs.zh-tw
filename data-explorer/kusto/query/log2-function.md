---
title: log2() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 log2()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 41e9a1457f97fa04a4daa54e1929f27d8a448ae3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513119"
---
# <a name="log2"></a>log2()

`log2()`返回基-2對數函數。  

**語法**

`log2(`*X.*`)`

**引數**

* *x*: > 0 的實數。

**傳回**

* 對數是基-2對數:指數函數(exp)與基2的反向。
* `null`如果參數為負或 null 或無法`real`轉換為值。 

**另請參閱**

* 有關自然(基-e)對數,請參閱[log()](log-function.md)。
* 有關公共(基-10)對數,請參閱[log10()](log10-function.md)。