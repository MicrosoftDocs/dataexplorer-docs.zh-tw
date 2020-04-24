---
title: .顯示函數 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .show 函數。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7acfdb96570b067b0461f5a788b55fc8274c03f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519834"
---
# <a name="show-functions"></a>.顯示函數

列出當前選擇的資料庫中的所有儲存函數。
要只傳回一個特定函數,請參閱[.show 函數](#show-function)。

```
.show functions
```

需要[資料庫使用者權限](../management/access-control/role-based-authorization.md)。
 
|輸出參數 |類型 |描述
|---|---|--- 
|名稱  |String |函數的名稱。 
|參數  |String |函數所需的參數。
|body  |String |(零或更多)`let`語句後跟在函數調用時計算的有效 CSL 運算式。
|資料夾|String|用於 UI 函數分類的資料夾。 此參數不會更改呼叫函數的方式。
|文件字串|String|用於 UI 目的的函數說明。
 
**輸出範例** 

|名稱 |參數|body|資料夾|文件字串
|---|---|---|---|---
|我的功能1 |() | [風暴事件&#124;限制 100}|我的資料夾|簡易簡報功能|
|My功能2 |(我的限制:長)| [風暴事件&#124;限制我的限制]|我的資料夾|帶參數的簡報|
|我的功能3 |() | • 風暴事件 (100) ||我的資料夾|函式呼叫其他函式||

## <a name="show-function"></a>.顯示功能

```
.show function MyFunc1
```

列出一個特定存儲函數的詳細資訊。 有關**所有**函數的清單,請參閱[.show 函數](#show-functions)。

**語法**

`.show``function`*函數名稱*

**輸出**

|輸出參數 |類型 |描述
|---|---|--- 
|名稱  |String |函數的名稱。 
|參數  |String |函數所需的參數。
|body  |String |(零或更多)`let`語句後跟在函數調用時計算的有效 CSL 運算式。
|資料夾|String|用於 UI 函數分類的資料夾。 此參數不會變更呼叫函數的方式
|文件字串|String|用於 UI 目的的函數說明。
 
> [!NOTE] 
> * 如果該函數不存在,則返回錯誤。
> * 需要[資料庫使用者權限](../management/access-control/role-based-authorization.md)。
 
**範例** 

```
.show function MyFunction1 
```
    
|名稱 |參數 |body|資料夾|文件字串
|---|---|---|---|---
|我的功能1 |() | [風暴事件&#124;限制 100}|我的資料夾|簡易簡報功能