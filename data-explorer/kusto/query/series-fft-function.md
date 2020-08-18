---
title: 'series_fft ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 函式 series_fft。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: 32b3b978f57f1a346ccd697fa2d95d962b558a15
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502118"
---
# <a name="series_fft"></a>series_fft ( # A1

在數列上 (FFT) 套用 Fast 傅立葉轉換。  

Series_fft ( # A1 函式會在時間/空間網域中使用一連串的複數，並使用 [快速傅立葉轉換](https://en.wikipedia.org/wiki/Fast_Fourier_transform)將它轉換成頻率網域。 轉換的複雜數列代表出現在原始數列中的頻率大小和階段。 使用互補函式 [series_ifft](series-ifft-function.md) 將 frequency 網域轉換回時間/空間網域。

## <a name="syntax"></a>語法

`series_fft(`*x_real* [ `,` *x_imaginary*]`)`

## <a name="arguments"></a>引數

* *x_real*：數值的動態陣列，代表要轉換之數列的實際元件。
* *x_imaginary*：類似的動態陣列，表示數列的虛陣列件。 這個參數是選擇性的，只有在輸入數列包含複數時，才應該指定。

## <a name="returns"></a>傳回

函數會傳回兩個數列中的複雜反向 fft。 實際元件的第一個數列，以及虛構元件的第二個數列。

## <a name="example"></a>範例

* 產生複雜的數列，其中 real 和虛陣列件是不同頻率的純正弦波。 使用 FFT 將它轉換為 frequency 網域：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let sinewave=(x:double, period:double, gain:double=1.0, phase:double=0.0)
    {
        gain*sin(2*pi()/period*(x+phase))
    }
    ;
    let n=128;      //  signal length
    range x from 0 to n-1 step 1 | extend yr=sinewave(x, 8), yi=sinewave(x, 32)
    | summarize x=make_list(x), y_real=make_list(yr), y_imag=make_list(yi)
    | extend (fft_y_real, fft_y_imag) = series_fft(y_real, y_imag)
    | render linechart with(ysplit=panels)
    ```
    
    此查詢會傳回 *fft_y_real* 和 *fft_y_imag*：  
    
    :::image type="content" source="images/series-fft-function/series-fft.png" alt-text="系列 fft" border="false":::
    
* 將數列轉換成 frequency 網域，然後套用反向轉換來取回原始數列：

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let sinewave=(x:double, period:double, gain:double=1.0, phase:double=0.0)
    {
        gain*sin(2*pi()/period*(x+phase))
    }
    ;
    let n=128;      //  signal length
    range x from 0 to n-1 step 1 | extend yr=sinewave(x, 8), yi=sinewave(x, 32)
    | summarize x=make_list(x), y_real=make_list(yr), y_imag=make_list(yi)
    | extend (fft_y_real, fft_y_imag) = series_fft(y_real, y_imag)
    | extend (y_real2, y_image2) = series_ifft(fft_y_real, fft_y_imag)
    | project-away fft_y_real, fft_y_imag   //  too many series for linechart with panels
    | render linechart with(ysplit=panels)
    ```
    
    此查詢會傳回與*y_real*和*y_imag*相同的*y_real2*和 * y_imag2：  
    
    :::image type="content" source="images/series-fft-function/series-ifft.png" alt-text="系列 ifft" border="false":::
    