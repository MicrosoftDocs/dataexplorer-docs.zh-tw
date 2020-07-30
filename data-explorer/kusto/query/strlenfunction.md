---
title: strlen （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 strlen （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d28eae6852faedf2c6071164d8f80f9c3567602
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350888"
---
# <a name="strlen"></a>strlen()

傳回輸入字串的長度（以字元為單位）。

## <a name="syntax"></a>語法

`strlen(`*來源*`)`

## <a name="arguments"></a>引數

* *source*：將針對字串長度測量的來源字串。

## <a name="returns"></a>傳回

傳回輸入字串的長度（以字元為單位）。

**備註**

字串中的每個 Unicode 字元都等於 `1` ，包括代理項。
（例如：即使在 UTF-8 編碼中，中文字元需要一個以上的值，也會計算一次。


## <a name="examples"></a>範例

```kusto
print length = strlen("hello")
```

|長度|
|---|
|5|

```kusto
print length = strlen("⒦⒰⒮⒯⒪")
```

|長度|
|---|
|5|