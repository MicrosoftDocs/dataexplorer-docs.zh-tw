---
title: 預存函數管理總覽-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的預存函式管理總覽。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 4354538b206ee86e34941718bcf6d74130647fb6
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321347"
---
# <a name="stored-functions-management-overview"></a>預存函數管理概觀
本節說明用來建立和改變下列 [使用者定義函數](../query/functions/user-defined-functions.md)的控制命令：

|函式 |描述|
|---------|-----------|
|[`.alter function`](alter-function.md) |改變現有的函式，並將它儲存在資料庫中繼資料中 |
|[`.alter function docstring`](alter-docstring-function.md) |改變現有函數的 DocString 值 |
|[`.alter function folder`](alter-folder-function.md) |改變現有函數的資料夾值 |
|[`.create function`](create-function.md) |建立預存函式 |
|[`.create-or-alter function`](create-alter-function.md) |建立預存函式，或改變現有的函式，並將它儲存在資料庫中繼資料內。 |
|[`.drop function` 和 `.drop functions`](drop-function.md) |從資料庫卸載函數 (或函數)  |
|[`.show functions` 和 `.show function`](show-function.md) |列出目前所選資料庫中的所有預存函式或特定函數 |