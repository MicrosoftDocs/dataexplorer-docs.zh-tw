---
title: RowLevelSecurity 原則（預覽）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 RowLevelSecurity 原則（預覽）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: 2df8178bbc75b9338699c00cdd8a16e7ab3b057f
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967514"
---
# <a name="row_level_security-policy-command-preview"></a>row_level_security 原則命令（預覽）

本文說明用來設定資料庫資料表之[row_level_security 原則](rowlevelsecuritypolicy.md)的命令。

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

即使原則已停用，您仍可以強制它套用至單一查詢。 在查詢之前輸入下面這一行：

`set query_force_row_level_security;`

如果您想要嘗試 row_level_security 的各種查詢，但不想要對使用者實際生效，這會很有用。 一旦您確信查詢，請啟用此原則。

> [!NOTE]
> 下列限制適用于 `query` ：
>
> * 查詢應該會產生與原則定義所在之資料表完全相同的架構。 也就是說，查詢的結果應該會以相同的名稱和類型，以相同的順序傳回與原始資料表完全相同的資料行。
> * 查詢只能使用下列運算子： `extend` 、 `where` 、 `project` 、 `project-away` 、、 `project-rename` `project-reorder` `join` 和 `union` 。
> * 查詢無法參考已啟用 RLS 的其他資料表。
> * 查詢可以是下列任何一項或兩者的組合：
>    * 查詢（例如， `<table_name> | extend CreditCardNumber = "****"` ）
>    * 函數（例如， `AnonymizeSensitiveData` ）
>    * Datatable （例如， `datatable(Col1:datetime, Col2:string) [...]` ）

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

**效能**提示： `UserCanSeeFullNumbers` 將先評估，然後再 `AllData` 評估或， `PartialData` 但不是兩者都是預期的結果。
您可以在[這裡](rowlevelsecuritypolicy.md#performance-impact-on-queries)閱讀有關 RLS 的效能影響的詳細資訊。

## <a name="deleting-the-policy"></a>正在刪除原則

```kusto
.delete table <table_name> policy row_level_security
```
