---
title: 行訂單策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的行順序策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 4dca1a4083a6141eb94d9bf3cf69f7134ebfca04
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520208"
---
# <a name="row-order-policy"></a>資料列順序原則

行順序策略是表上的可選策略,它向 Kusto 提供「提示」數據分片中所需的行的順序。

策略的主要目的是不是改進壓縮(儘管它是一個潛在的副作用),而是為了提高已知被縮小到有序列中一小部分值的查詢的性能。

在以下情況下應用策略是合適的:
* 大多數查詢篩選特定大尺寸列的特定值(如"應用程式 ID"或「租戶 ID」)
* 根據此列,引入表中的數據不太可能被預先排序。

雖然對可定義為策略一部分的列(排序鍵)數量沒有設置硬編碼限制,但每增加一列都會為引入過程增加一些開銷,並且隨著添加更多列-有效返回將會減少。

> [!NOTE]
> 一旦策略應用於表,它將影響從該時刻開始引入的數據。

管理列訂單原則的控制命令[可在此處](../management/roworder-policy.md)找到