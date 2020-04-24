---
title: sqrt() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 sqrt()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 660235a60893732288a551e1febd9b7b044b4f00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507254"
---
# <a name="sqrt"></a>sqrt()

返回平方根函數。  

**語法**

`sqrt(`*X.*`)`

**引數**

* *x*: >= 0 的實數。

**傳回**

* 像是 `sqrt(x) * sqrt(x) == x`
* 如果引數為負數或無法轉換為 `real` 值，則為 `null`。 