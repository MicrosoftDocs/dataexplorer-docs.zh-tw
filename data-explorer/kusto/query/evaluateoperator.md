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
ms.openlocfilehash: 519ac6b38a73cfc7334094ef503d1d20c7d2ecb9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348219"
---
# <a name="evaluate-operator-plugins"></a>evaluate 運算子外掛程式

叫用服務端查詢延伸模組（外掛程式）。

`evaluate`運算子是一種表格式運算子，可讓您叫用稱為**外掛程式**的查詢語言擴充功能。 外掛程式可以啟用或停用（不同于一律可供使用的其他語言結構），而且不會受到語言的關聯式本質的「系結」（例如，它們可能沒有預先定義、靜態判斷的輸出架構）。

## <a name="syntax"></a>語法 

[*T* `|` ] `evaluate` [ *evaluateParameters* ] *PluginName* `(` [*PluginArg1* [ `,` *PluginArg2*] .。。`)`

其中：

* *T*是外掛程式的選擇性表格式輸入。 （有些外掛程式不會接受任何輸入，而是作為表格式資料來源）。
* *PluginName*是要叫用之外掛程式的必要名稱。
* *PluginArg1*，.。。是外掛程式的選擇性引數。
* *evaluateParameters*：*名稱*值格式為零或多個（以空格分隔）的參數 `=` *Value* ，可控制評估作業和執行計畫的行為。 每個外掛程式可能會以不同的方式來決定如何處理每個參數。 請參閱每個外掛程式的檔，以瞭解特定的行為。  

支援下列參數： 

  |名稱                |值                           |說明                                |
  |--------------------|---------------------------------|-------------------------------------------|
  |`hint.distribution` |`single`, `per_node`, `per_shard`| [散發提示](#distributionhints) |
  |`hint.pass_filters` |`true`, `false`| 允許 `evaluate` 運算子在外掛程式之前傳遞任何相符的篩選準則。 如果參考的資料行是在運算子之前，則會將篩選視為「相符」 `evaluate` 。 預設： `false` |
  |`hint.pass_filters_column` |*column_name*| 允許外掛程式操作員傳遞在外掛程式之前參考*column_name*的篩選準則。 參數可以多次使用，並使用不同的資料行名稱。 |

**備註**

* 在語法上，其 `evaluate` 行為類似于叫用表格式函數的叫用[運算子](./invokeoperator.md)。
* 透過評估運算子提供的外掛程式不會受到查詢執行或引數評估的一般規則所限制。
* 特定外掛程式可能會有特定的限制。 例如，執行跨叢集查詢時，不能使用其輸出架構相依于資料的外掛程式（例如[bag_unpack 外掛程式](./bag-unpackplugin.md)和[資料透視外掛程式](./pivotplugin.md)）。

<a id="distributionhints"/>**散發提示**</a>

發佈提示會指定外掛程式執行如何分散到多個叢集節點。 每個外掛程式都可能會對散發執行不同的支援。 外掛程式的檔會指定外掛程式支援的散發選項。

可能的值：

* `single`：外掛程式的單一實例將會在整個查詢資料上執行。
* `per_node`：如果在外掛程式呼叫之前的查詢分散到各個節點，則外掛程式的實例將會透過其包含的資料在每個節點上執行。
* `per_shard`：如果外掛程式呼叫之前的資料分散在分區上，則外掛程式的實例將會在每個分區的資料上執行。
