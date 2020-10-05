---
title: 查詢節流原則命令-Azure 資料總管
description: 本文說明 Azure 中的查詢節流原則命令資料總管
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 85408aadfd15fc48f1e3e86ab01afce5dca15bce
ms.sourcegitcommit: acd8aba5ed957bee4a7361057387145f9d3ef4e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91733991"
---
# <a name="query-throttling-policy-commands"></a>查詢節流原則命令

[查詢節流原則](query-throttling-policy.md)是一種叢集層級原則，可限制叢集中的並行查詢。 這些查詢節流原則命令需要 [AllDatabasesAdmin](../management/access-control/role-based-authorization.md) 許可權。

## <a name="query-throttling-policy-object"></a>查詢節流原則物件

叢集可能已定義零個或一個查詢節流原則。

當叢集未定義查詢節流原則時，就會套用預設原則。 如需預設原則的詳細資訊，請參閱 [查詢限制](../concepts/querylimits.md)。

|屬性  |類型    |描述                                                       |
|----------|--------|------------------------------------------------------------------|
|IsEnabled |`bool`  |指出是否已啟用查詢節流原則 (true) 或停用 (false) 。     |
|MaxQuantity|`int`|叢集可以執行的並行查詢數目。 數位的值必須是正數。 |

## `.show cluster policy querythrottling`

傳回叢集的 [查詢節流原則](query-throttling-policy.md) 。

### <a name="syntax"></a>Syntax

`.show` `cluster` `policy` `querythrottling`

### <a name="returns"></a>傳回

傳回包含下列資料行的資料表：

|資料行    |類型    |描述
|---|---|---
|PolicyName| 字串 |原則名稱-QueryThrottlingPolicy
|EntityName| 字串 |Empty
|原則    | 字串 |定義查詢節流原則的 JSON 物件，格式化為 [查詢節流原則物件](#query-throttling-policy-object)

### <a name="example"></a>範例

<!-- csl -->
```
.show cluster policy querythrottling 
```

傳回：

|PolicyName|EntityName|原則|ChildEntities|EntityType|
|---|---|---|---|---|
|QueryThrottlingPolicy||{"IsEnabled"： true，"MaxQuantity"： 25}

## `.alter cluster policy querythrottling`

設定指定之資料表的 [查詢節流原則](query-throttling-policy.md) 。 

### <a name="syntax"></a>Syntax

`.alter``cluster` `policy` `querythrottling`*QueryThrottlingPolicyObject*

### <a name="arguments"></a>引數

*QueryThrottlingPolicyObject* 是已定義查詢節流原則物件的 JSON 物件。

### <a name="returns"></a>傳回

設定叢集查詢節流原則物件，覆寫任何目前定義的原則，然後傳回對應的輸出（[顯示叢集原則 `querythrottling` ](#show-cluster-policy-querythrottling)命令）。

### <a name="example"></a>範例

<!-- csl -->
```
.alter cluster policy querythrottling '{"IsEnabled": true, "MaxQuantity": 25}'
```

|PolicyName|EntityName|原則|ChildEntities|EntityType|
|---|---|---|---|---|
|QueryThrottlingPolicy||{"IsEnabled"： true，"MaxQuantity"： 25}

## `.delete cluster policy querythrottling`

卸載叢集 [查詢節流原則](query-throttling-policy.md) 物件。

### <a name="syntax"></a>Syntax

`.delete` `cluster` `policy` `querythrottling`
