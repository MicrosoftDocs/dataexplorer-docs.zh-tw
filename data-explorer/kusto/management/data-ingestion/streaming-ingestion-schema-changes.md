---
title: 串流內嵌和架構變更-Azure 資料總管
description: 本文討論使用 Azure 資料總管中的串流內嵌來處理架構變更的選項。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/20/2020
ms.openlocfilehash: f1ab3d012be7751eba33447b325748859509af00
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784560"
---
# <a name="streaming-ingestion-and-schema-changes"></a>串流內嵌和架構變更

## <a name="background"></a>背景

叢集節點會快取透過串流內嵌接收資料之資料庫的架構。 此程式可優化叢集資源的效能和使用，但可能會在架構變更時造成傳播延遲。

架構變更的範例如下：

* 建立和刪除資料庫和資料表
* 加入、移除、重新鍵入或重新命名資料表的資料行
* 新增或移除預先建立的內嵌對應
* 新增、移除或改變原則

如果架構變更和串流內嵌流程未經協調，某些串流內嵌要求可能會失敗。 失敗可能包括架構相關錯誤，或將未完成或失真的資料插入資料表。
在執行自訂的內嵌應用程式時，強烈建議您在限定時間執行重試，或透過佇列的內嵌方法，從失敗的要求重新路由資料，以處理架構相關的失敗。

## <a name="clearing-the-schema-cache"></a>清除架構快取

明確清除叢集節點上的架構快取，以降低傳播延遲的效果。
使用其中一個清除架構快取來清除架構快取，[以進行串流](clear-schema-cache-command.md)內嵌管理命令。
如果串流處理內嵌流程和架構變更是協調的，您可以完全排除失敗及其相關聯的資料扭曲。 

**協調流程範例：**

1. 暫止串流內嵌。
1. 等候所有未完成的串流內嵌要求完成>
1. 進行架構變更。
1. 發出一或多個 `.clear cache streaming ingestion` 架構命令。 
    * 重複直到成功，且命令輸出中的所有資料列都指出成功
1. 繼續串流內嵌。

> [!NOTE]
> 使用清除快取串流內嵌架構命令，可能會對串流內嵌的效能造成負面影響。
