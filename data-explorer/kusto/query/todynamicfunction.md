---
title: 到動態(),到物件() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的"動態"到 物件()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 138b0f978df699817c5dc5c14bafc4c06a95afc7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506149"
---
# <a name="todynamic-toobject"></a>到動態(),到物件()

將 解`string`譯為[JSON](https://json.org/)值[`dynamic`](./scalar-data-types/dynamic.md),並將該值傳回為 。 

當您需要提取 JSON 複合物件的多個元素時,它優於使用[extractjson() 函數](./extractjsonfunction.md)。

別名到[parse_json()函數](./parsejsonfunction.md)。

**語法**

`todynamic(`*json*`)`傑
`toobject(`森 *·傑森*`)`

**引數**

* *json*: JSON 文檔。

**傳回**

json** 所指定類型為 `dynamic` 的物件。

*注意*:盡可能使用[動態()。](./scalar-data-types/dynamic.md)