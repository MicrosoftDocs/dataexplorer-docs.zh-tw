---
title: 'series_ifft ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 函式 series_ifft。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: c5862e9c18959726f27ea7d4237058f36a408b5c
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502115"
---
# <a name="series_ifft"></a>series_ifft ( # A1

在數列上 (IFFT) 套用反向的傅立葉轉換。  

Series_ifft ( # A1 函式會在 frequency 網域中使用一連串的複數，並使用 [快速傅立葉轉換](https://en.wikipedia.org/wiki/Fast_Fourier_transform)將它轉換回時間/空間網域。 此函數是 [series_fft](series-fft-function.md)的互補函數。 原始數列通常會轉換成頻率網域以進行光譜處理，然後再轉換回時間/空間網域。

## <a name="syntax"></a>語法

`series_ifft(`*fft_real* [ `,` *fft_imaginary*]`)`

## <a name="arguments"></a>引數

* *fft_real*：數值的動態陣列，代表要轉換之數列的實際元件。
* *fft_imaginary*：類似的動態陣列，表示數列的虛陣列件。 這個參數是選擇性的，只有在輸入數列包含複數時，才應該指定。

## <a name="returns"></a>傳回

函數會傳回兩個數列中的複雜反向 fft。 實際元件的第一個數列，以及虛構元件的第二個數列。

## <a name="example"></a>範例

請參閱 [series_fft](series-fft-function.md#example)
