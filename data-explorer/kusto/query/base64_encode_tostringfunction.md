---
title: base64_encode_tostring() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的base64_encode_tostring()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: a80b0aa0e3f7e5f330da87f93bbad44e587bcdaf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518049"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

將字串編碼為 base64 字串

**語法**

`base64_encode_tostring(`*字串*`)`

**引數**

* *字串*:輸入字串,以編碼為 base64 字串。

**傳回**

傳回編碼為 base64 字串的字串。

* 有關將基64字串解碼為 UTF-8 字串,請參閱[base64_decode_tostring()](base64_decode_tostringfunction.md)
* 要將 base64 字串解碼為長值陣列,請參閱[base64_decode_toarray()](base64_decode_toarrayfunction.md)


**範例**

```kusto
print Quine=base64_encode_tostring("Kusto")
```

|奎因   |
|--------|
|S3VzdG8*|
