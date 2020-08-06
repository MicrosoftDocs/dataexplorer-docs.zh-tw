---
title: 'todynamic ( # A1、toobject ( # A3-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 todynamic ( # A1、toobject ( # A3。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: acd1b5328150e61bc81930f94b8ea9e8025e1ebb
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804078"
---
# <a name="todynamic-toobject"></a>todynamic(), toobject()

將解讀 `string` 為[JSON 值](https://json.org/)，並以形式傳回值 [`dynamic`](./scalar-data-types/dynamic.md) 。 

當您需要解壓縮 JSON 綜合物件的多個專案時，使用[extractjson ( # A1](./extractjsonfunction.md)函式是較佳的功能。

[Parse_json ( # B1](./parsejsonfunction.md)函數的別名。

> [!NOTE]
> 盡可能使用[動態 ( # B1](./scalar-data-types/dynamic.md) 。

## <a name="syntax"></a>語法

`todynamic(`*json* `)` 
 json `toobject(`*json*`)`

## <a name="arguments"></a>引數

* *json*： json 檔。

## <a name="returns"></a>傳回

json** 所指定類型為 `dynamic` 的物件。
