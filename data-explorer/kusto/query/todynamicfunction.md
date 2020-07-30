---
title: todynamic （）、toobject （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 todynamic （）、toobject （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e11a4d450275fb4d596bd9618c20ef6cefcb0531
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350721"
---
# <a name="todynamic-toobject"></a>todynamic(), toobject()

將解讀 `string` 為[JSON 值](https://json.org/)，並以形式傳回值 [`dynamic`](./scalar-data-types/dynamic.md) 。 

當您需要解壓縮 JSON 綜合物件的多個專案時，使用[extractjson （）](./extractjsonfunction.md)函式是較佳的功能。

[Parse_json （）](./parsejsonfunction.md)函數的別名。

## <a name="syntax"></a>語法

`todynamic(`*json* `)` 
 json `toobject(`*json*`)`

## <a name="arguments"></a>引數

* *json*： json 檔。

## <a name="returns"></a>傳回

json** 所指定類型為 `dynamic` 的物件。

*注意*：可能的話，建議使用[動態（）](./scalar-data-types/dynamic.md) 。