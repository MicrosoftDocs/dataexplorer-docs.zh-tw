---
title: 評估外掛程式操作員-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的評估外掛程式操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: dc7e410b79ad73c1ba9fa807142177f4270b7dfa
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803109"
---
# <a name="evaluate-operator-plugins"></a>evaluate 運算子外掛程式

叫用服務端查詢延伸模組 (外掛程式) 。

`evaluate`運算子是一種表格式運算子，可讓您叫用稱為**外掛程式**的查詢語言擴充功能。 可以啟用或停用外掛程式 (不同于其他語言結構，這些架構一律可供) ，而不是由語言的關聯式性質所「系結」 (例如，它們可能沒有預先定義、靜態判斷的輸出架構) 。

> [!NOTE]
> * 在語法上，其 `evaluate` 行為類似于叫用表格式函數的叫用[運算子](./invokeoperator.md)。
> * 透過評估運算子提供的外掛程式不會受到查詢執行或引數評估的一般規則所限制。
> * 特定外掛程式可能會有特定的限制。 例如，如果外掛程式的輸出架構相依于資料 (例如， [bag_unpack 外掛程式](./bag-unpackplugin.md)和[樞紐分析表外掛程式](./pivotplugin.md)) 無法在執行跨叢集查詢時使用。

## <a name="syntax"></a>語法 

[*T* `|` ] `evaluate` [ *evaluateParameters* ] *PluginName* `(` [*PluginArg1* [ `,` *PluginArg2*] .。。`)`

## <a name="arguments"></a>引數

* *T*是外掛程式的選擇性表格式輸入。  (某些外掛程式不會接受任何輸入，而是作為表格式資料來源。 ) 
* *PluginName*是要叫用之外掛程式的必要名稱。
* *PluginArg1*，.。。是外掛程式的選擇性引數。
* *evaluateParameters*：零個或多個 (以空格分隔的) 參數，格式為*Name* `=` *值*，可控制評估作業和執行計畫的行為。 每個外掛程式可能會以不同的方式來決定如何處理每個參數。 請參閱每個外掛程式的檔，以瞭解特定的行為。  

## <a name="parameters"></a>參數

支援下列參數： 

  |名稱                |值                           |說明                                |
  |--------------------|---------------------------------|-------------------------------------------|
  |`hint.distribution` |`single`, `per_node`, `per_shard`| [散發提示](#distribution-hints) |
  |`hint.pass_filters` |`true`, `false`| 允許 `evaluate` 運算子在外掛程式之前傳遞任何相符的篩選準則。 如果參考的資料行是在運算子之前，則會將篩選視為「相符」 `evaluate` 。 預設： `false` |
  |`hint.pass_filters_column` |*column_name*| 允許外掛程式操作員傳遞在外掛程式之前參考*column_name*的篩選準則。 參數可以多次使用，並使用不同的資料行名稱。 |

## <a name="distribution-hints"></a>散發提示

發佈提示會指定外掛程式執行如何分散到多個叢集節點。 每個外掛程式都可能會對散發執行不同的支援。 外掛程式的檔會指定外掛程式支援的散發選項。

可能的值：

* `single`：外掛程式的單一實例將會在整個查詢資料上執行。
* `per_node`：如果在外掛程式呼叫之前的查詢分散到各個節點，則外掛程式的實例將會透過其包含的資料在每個節點上執行。
* `per_shard`：如果外掛程式呼叫之前的資料分散在分區上，則外掛程式的實例將會在每個分區的資料上執行。
