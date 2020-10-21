---
title: 'todynamic ( # A1，toobject ( # A3-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 todynamic ( # A1、toobject ( # A3。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c92ff96b3ae6a0369de1c8fa715e64499fb051b7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250492"
---
# <a name="todynamic-toobject"></a>todynamic(), toobject()

將 `string` 做為 [JSON 值](https://json.org/) ，並傳回做為的值 [`dynamic`](./scalar-data-types/dynamic.md) 。 

當您需要解壓縮 JSON 綜合物件的多個元素時，最好使用 [extractjson ( # A1 函數](./extractjsonfunction.md) 。

[Parse_json ( # B1](./parsejsonfunction.md)函數的別名。

> [!NOTE]
> 最好盡可能使用 [動態 ( # B1 ](./scalar-data-types/dynamic.md) 。

## <a name="syntax"></a>語法

`todynamic(`*json* `)` 
 json `toobject(`*json*`)`

## <a name="arguments"></a>引數

* *json*： json 檔。

## <a name="returns"></a>傳回

json** 所指定類型為 `dynamic` 的物件。
