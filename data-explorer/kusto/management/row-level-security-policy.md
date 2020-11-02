---
title: RowLevelSecurity 原則-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 RowLevelSecurity 原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/11/2020
ms.openlocfilehash: 25ad7040b0318206a712a9a7fb8d3be58e0f47f3
ms.sourcegitcommit: 0e2fbc26738371489491a96924f25553a8050d51
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148434"
---
# <a name="row_level_security-policy-command"></a>row_level_security 原則命令

本文說明用來設定資料庫資料表之 [row_level_security 原則](rowlevelsecuritypolicy.md) 的命令。

## <a name="displaying-the-policy"></a>顯示原則

若要顯示原則，請使用下列命令：

```kusto
.show table <table_name> policy row_level_security
```

## <a name="configuring-the-policy"></a>設定原則

在資料表上啟用 row_level_security 原則：

```kusto
.alter table <table_name> policy row_level_security enable "<query>"
```

停用資料表上的 row_level_security 原則：

```kusto
.alter table <table_name> policy row_level_security disable "<query>"
```

即使在停用原則的情況下，您也可以強制將它套用至單一查詢。 在查詢之前輸入下面這一行：

`set query_force_row_level_security;`

如果您想要嘗試 row_level_security 的各種查詢，但不想讓它實際對使用者生效，這會很有用。 一旦您對查詢感到安心，請啟用此原則。

> [!NOTE]
> 下列限制適用于 `query` ：
>
> * 查詢應該會產生與定義原則的資料表完全相同的架構。 也就是說，查詢的結果應該以相同的順序傳回與原始資料表完全相同的資料行，且具有相同的名稱和類型。
> * 查詢只能使用下列運算子： `extend` 、 `where` 、 `project` 、 `project-away` 、 `project-keep` 、 `project-rename` 、 `project-reorder` `join` 和 `union` 。
> * 查詢無法參考啟用 RLS 的其他資料表。
> * 查詢可以是下列任何一項，也可以是它們的組合：
>    * 查詢 (例如， `<table_name> | extend CreditCardNumber = "****"`) 
>    * 函數 (例如 `AnonymizeSensitiveData`) 
>    * Datatable (例如 `datatable(Col1:datetime, Col2:string) [...]`) 

> [!TIP]
> 這些函數通常適用于 row_level_security 查詢：
> * [current_principal()](../query/current-principalfunction.md)
> * [current_principal_details()](../query/current-principal-detailsfunction.md)
> * [current_principal_is_member_of()](../query/current-principal-ismemberoffunction.md)

**範例**

```kusto
.create-or-alter function with () TrimCreditCardNumbers() {
    let UserCanSeeFullNumbers = current_principal_is_member_of('aadgroup=super_group@domain.com');
    let AllData = Customers | where UserCanSeeFullNumbers;
    let PartialData = Customers | where not(UserCanSeeFullNumbers) | extend CreditCardNumber = "****";
    union AllData, PartialData
}

.alter table Customers policy row_level_security enable "TrimCreditCardNumbers"
```

**效能注意事項** ： `UserCanSeeFullNumbers` 將會先評估，然後才會 `AllData` 評估或， `PartialData` 但不是兩者都是預期的結果。
您可以在 [這裡](rowlevelsecuritypolicy.md#performance-impact-on-queries)閱讀有關 RLS 效能影響的詳細資訊。

## <a name="deleting-the-policy"></a>刪除原則

```kusto
.delete table <table_name> policy row_level_security
```
