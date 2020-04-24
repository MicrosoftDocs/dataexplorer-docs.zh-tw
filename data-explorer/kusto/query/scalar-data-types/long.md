---
title: 長資料類型 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的長數據類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b8c51f216b8515e483daf64d9783210ff35ceef3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509702"
---
# <a name="the-long-data-type"></a>長資料類型

數據類型`long`表示一個已簽名的 64 位寬整數。

## <a name="long-literals"></a>長字面

資料型態`long`的 文字可以在以下文法中指定:

`long``(`*值*`)`

*價值*可以採取以下形式:
* 多一位數位或數位,在這種情況下,文本值是這些數位的十進位表示形式。 例如,`long(12)`是類型的第十`long`二個數位。
* 首碼`0x`後跟一個或多個十六進位數位。 例如，`long(0xf)` 等於 `long(15)`。
* 減號`-`( ) 符號後跟一個或多個數位。 例如,`long(-1)`是`long`類型 中的數字減去 1。
* `null`,在這種情況下,這是資料類型的`long` [null 值](null-values.md)。 因此,類型的`long`空值為`long(null)`。

Kusto`long`還支援類型文本`long(`/`)`, 而前兩種形式沒有前綴/suffi。 因此,`123``long`是類型的文本,如所`0x123`示`-2`,**但不是**文本(它當前被解釋為應用於`-``2`類型長文本 的一元函數)。
 
要將長轉換為十六進位元字串 ─ 請參考[tox() 函數](../tohexfunction.md)。