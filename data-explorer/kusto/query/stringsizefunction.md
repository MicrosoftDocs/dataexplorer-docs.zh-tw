---
title: string_size （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 string_size （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4832d00703ab9e6d1478af5b3f45ec294dfb79ce
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350905"
---
# <a name="string_size"></a>string_size()

傳回輸入字串的大小（以位元組為單位）。

## <a name="syntax"></a>語法

`string_size(`*來源*`)`

## <a name="arguments"></a>引數

* *source*：將針對字串大小測量的來源字串。

## <a name="returns"></a>傳回

傳回輸入字串的長度（以位元組為單位）。

## <a name="examples"></a>範例

```kusto
print size = string_size("hello")
```

|大小|
|---|
|5|

```kusto
print size = string_size("⒦⒰⒮⒯⒪")
```

|大小|
|---|
|15|