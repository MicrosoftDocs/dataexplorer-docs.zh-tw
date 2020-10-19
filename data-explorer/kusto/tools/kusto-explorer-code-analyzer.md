---
title: Kusto Explorer 程式碼分析器-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 Kusto Explorer 程式碼分析器。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 058ba490727d50ca8c45252414dcbf94e2e20e8e
ms.sourcegitcommit: 88923cfb2495dbf10b62774ab2370b59681578b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92175584"
---
# <a name="kusto-explorer-code-analyzer"></a>Kusto Explorer 程式碼分析器

Kusto 會提供程式碼分析器公用程式來分析目前的查詢，並輸出一組適用的改進建議。 

使用在使用中的 `Ctrl` + `F6` 查詢上執行分析器。

:::image type="content" source="images/kusto-explorer-code-analyzer/ke-code-analyze.gif" alt-text="Kusto Explorer 中的程式碼分析器 GIF":::