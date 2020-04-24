---
title: 評估外掛程式運算子 ─ Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的評價外掛程式運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 1aae36df29abf705ba821abdc2d1da96e4635a60
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515737"
---
# <a name="evaluate-plugin-operator"></a>evaluate 外掛程式運算子

調用服務端查詢擴展(外掛程式)。

運算子`evaluate`是一個表格運算符,提供調用稱為**外掛程式**的查詢語言擴展的能力。 外掛程式可以啟用或禁用(與其他語言構造始終可用不同),並且不受語言關係性質"綁定"(例如,它們可能沒有預定義的靜態確定輸出架構)。

**語法** 

[* * `|` `evaluate` T ] [*計算參數*`,` ]*外掛程式名稱*`(`•*外掛程式Arg1* •*外掛程式Arg2*[...`)`

其中：

* *T*是外掛程式的可選表格輸入。 (某些外掛程式不採用任何輸入,並充當表格數據來源。
* *外掛程式名稱*是被調用的外掛程式的必填名稱。
* *外掛程式Arg1*, ...是外掛程式的可選參數。
* *計算參數*:以*名稱*`=`*值*的形式控制計算操作和執行計劃的行為的零個或多個(空間分隔)參數。 支援下列參數： 

  |名稱                |值                           |描述                                |
  |--------------------|---------------------------------|-------------------------------------------|
  |`hint.distribution` |`single`, `per_node`, `per_shard`| [分轉提示](#distribution-hints) |

**注意事項**

* 從語法上講,`evaluate`其操作方式類似於[調用運算符](./invokeoperator.md),後者調用表格函數。
* 通過計算運算符提供的外掛程式不受查詢執行或參數計算的常規規則的約束。
特定的外掛程式可能有特定的限制。 例如,在執行跨群集查詢時,不能使用輸出架構依賴於數據的外掛程式(例如[,bag_unpack外掛程式](./bag-unpackplugin.md))。

## <a name="distribution-hints"></a>分轉提示

分發提示指定外掛程式執行將如何跨多個群集節點分佈。 每個外掛程式可以對分發實現不同的支援。 外掛程式的文件指定外掛程式支援的分發選項。

可能的值：

* `single`:外掛程式的單個實體執行在整個查詢資料上。
* `per_node`:如果外掛程式調用之前的查詢分佈在節點之間,則外掛程式的實例將在每個節點上運行,因為它包含的數據。
* `per_shard`:如果外掛程式調用之前的數據分佈在分片之間,則外掛程式的實例將運行在數據的每個分片上。