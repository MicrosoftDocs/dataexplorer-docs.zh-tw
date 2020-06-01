---
title: IngestionTime 原則-Azure 資料總管
description: 本文說明 Azure 資料總管中的 IngestionTime 原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 50e0083b1cdbed06106507fe69fb0d039c923c43
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257802"
---
# <a name="ingestiontime-policy"></a>IngestionTime 原則

IngestionTime 原則是選擇性的原則，可以在資料表上設定（啟用）。

啟用時，Kusto 會將隱藏的資料 `datetime` 行加入至資料表，名為 `$IngestionTime` 。 現在，每當新的資料內嵌時，內嵌的時間就會記錄在隱藏的資料行中。 這段時間是由 Kusto 叢集在認可資料之前進行測量。 

> [!NOTE]
> 每一筆記錄都有自己的 `$IngestionTime` 值。

因為 [內嵌時間] 資料行是隱藏的，所以您無法直接查詢其值。
相反地，稱為[ingestion_time （）](../query/ingestiontimefunction.md)的特殊函數會抓取該值。 如果 `datetime` 資料表中沒有資料行，或內嵌記錄時未啟用 IngestionTime 原則，則會傳回 null 值。

IngestionTime 原則是針對兩個主要案例所設計：
* 可讓使用者預估內嵌資料中的延遲。
  許多具有記錄資料的資料表都有時間戳記資料行。 時間戳記值會由來源填入，並指出產生記錄的時間。 藉由比較該資料行的值與 [內建時間] 資料行，您可以預估在中取得資料的延遲。 
  
  > [!NOTE]
  > 計算值只是預估，因為來源和 Kusto 不一定會同步處理其時鐘。
  
* 為了支援可讓使用者發出連續查詢的[資料庫資料指標](../management/databasecursor.md)，此查詢僅限於上一次查詢之後所內嵌的資料。

如需詳細資訊， 請參閱[管理 IngestionTime 原則的控制命令](../management/ingestiontime-policy.md)。
