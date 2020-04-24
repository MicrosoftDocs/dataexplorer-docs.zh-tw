---
title: 實體名稱 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的實體名稱。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: 2fecbd538a53d8eb02e55b0bd1def2186467b019
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509413"
---
# <a name="entity-names"></a>實體名稱

Kusto 實體(資料庫、表、列和存儲函數;群集是例外)被命名。 實體的名稱標識實體,並保證在其容器範圍內具有唯一性,給定其類型。
(例如,同一資料庫中的兩個表不能具有相同的名稱,但表和資料庫可能具有相同的名稱,因為它們不在同一作用域中,並且表和存儲的函數可能具有相同的名稱,因為它們不是同一實體類型。

實體名稱對於解決目的**區分大小寫**(因此,例如,不能`ThisTable`將稱為的表`thisTABLE`引用為 。

實體名稱是**識別碼**的一個範例。 其他標識符包括參數的名稱到函數,並通過[let語句](../letstatement.md)綁定名稱。

## <a name="entity-pretty-names"></a>實體漂亮名稱

除了實體名稱之外,某些實體(如資料庫)可能還有**一個漂亮的名稱**。 漂亮的名稱可用於引用查詢中的實體(如實體名稱),但與實體名稱不同,漂亮的名稱不一定在其容器的上下文中是唯一的。 當容器具有具有相同漂亮名稱的多個實體時,無法使用漂亮名稱來引用該實體。

漂亮的名稱允許中間層應用程式自動創建實體名稱(如 UUD)映射到用於顯示和引用目的的人可讀的名稱。

## <a name="identifier-naming-rules"></a>識別子命名規則

<!-- TODO: This section should be reviewed and moved to its own page -->

標識符用於命名各種實體(實體或其他實體)。
合法識別子名稱遵循以下規則:
* 它們有 1 到 1024 個字元長。
* 它們可能包含字母、數位、下劃線`_`()、空格、`.`點 () 和`-`破折號 ()。
  * 僅由字母、數位和下劃線組成的標識碼在引用標識符時不需要報價。
  * 包含最後一個(空格、點或破折號)的標識符確實需要報價(見下文)。
* 它們區分大小寫。

## <a name="identifier-quoting"></a>識別碼報價

與某些查詢語言關鍵字相同或具有上述特殊字元之一的標識符,在查詢直接引用時需要引用:

|查詢文字         |註解                          |
|-------------------|----------------------------------|
| `entity`          |不包含特殊字元`entity`或映射到某些語言關鍵字的實體名稱 () 不需要報價|
|`['entity-name']`  |包含特殊`-`字元(此處: ) 的實體名稱`['``']`必須`["`使用 與或使用與`"]`|
|`["where"]`        |為語言關鍵字的實體名稱必須參考,`['`使用`']`與或`["`使用`"]`|

## <a name="naming-your-entities-to-avoid-collisions-with-kusto-language-keywords"></a>命名實體以避免與 Kusto 語言關鍵字發生衝突

由於 Kusto 查詢語言包含許多與識別碼具有相同的命名規則的關鍵字,因此實體名稱實際上是關鍵字,但隨後很難引用這些名稱(必須引用它們)。

或者,您可能想要選擇保證永遠不會與 Kusto 關鍵字"碰撞" 的實體名稱。 提供以下保證:

1. Kusto 查詢語言不會定義以大寫字母`A`(`Z`到 ) 開頭的關鍵字。
2. Kusto 查詢語言不會定義以單下一個底線開頭的關鍵字`_`( . us
3. Kusto 查詢語言不會定義以單個下劃線`_`( ) 結尾的關鍵字。

Kusto 查詢語言保留以兩個下劃線字元`__`() 的序列開頭或結尾的所有識別碼。用戶無法定義此類名稱供自己使用。








