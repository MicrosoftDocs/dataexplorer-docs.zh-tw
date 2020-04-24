---
title: 邏輯(二進位)運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的邏輯(二進位)運算元。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/14/2018
ms.openlocfilehash: 53505067b93234aa89849e1c66fe2f77a58e0f26
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513085"
---
# <a name="logical-binary-operators"></a>邏輯 (二元) 運算子

以下邏輯運算子在`bool`類型的兩個值之間受支援:

> [!NOTE]
> 這些邏輯運算符有時稱為布爾運算符,有時也稱為二進位運算符。 名稱都是同義詞。

|操作員名稱|語法|意義|
|-------------|------|-------|
|等式     |`==`  |如果`true`兩個操作數都是非空且彼此相等,則生成。 否則為 `false`。|
|不等   |`!=`  |如果`true`操作數中的一個(或兩個)為空,或者它們彼此不相等,則生成。 否則為 `true`。|
|邏輯和  |`and` |如果`true`兩個操作數`true`都是 ,則生成。|
|邏輯或   |`or`  |`true`如果其中一個操作數是`true`,則生成,而不考慮其他操作數。|

> [!NOTE]
> 由於布爾值`bool(null)`null 值的行為,兩個布爾值空值既不相等也不相等(換句話`bool(null) == bool(null)`說`bool(null) != bool(null)`, 兩 者都`false`生成值)。
>
> 另`and`/`false``bool(null) or true``true``bool(null) and true``false`一方面,將 null 值視為等效值,則為與 。`or`