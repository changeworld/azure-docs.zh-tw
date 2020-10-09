---
title: Azure Kinect DK 深度相機
description: 瞭解您 Azure Kinect DK 中深度攝影機的操作原則和主要功能。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect、azure、感應器、sdk、深度相機、tof、原則、效能、失效
ms.openlocfilehash: 22f04b983ed7c6a2ab19a5c1c709621655ee31c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276486"
---
# <a name="azure-kinect-dk-depth-camera"></a>Azure Kinect DK 深度相機

本頁面涵蓋如何使用 Azure Kinect DK 中的深度相機。 深度相機是兩張攝影機的第二張。 如上一節所述，另一個攝影機是 RGB 相機。  

## <a name="operating-principles"></a>操作原則

Azure Kinect DK 深度相機會 (AMCW) 進行中的時間 (ToF) 準則，來實行調幅的「調幅」連續 Wave。 攝影機會將近 IR (NIR) 頻譜中的調製照明轉換成場景。 然後，它會記錄從相機傳送到場景和背面的光線時間的間接測量。

這些測量會經過處理，以產生深度對應。 深度對應是影像中每個圖元的一組 Z 座標值，以毫米單位為單位。

除了深度對應外，我們也會取得所謂的「乾淨 IR 讀取」。 清除 IR 讀取的圖元值與場景傳回的光線量成正比。 影像看起來類似一般 IR 映射。 下圖顯示的範例深度對應 (左) 和對應的清晰 IR 影像 (右) 。

![深度和 IR 並存](./media/concepts/depth-camera-depth-ir.png)

## <a name="key-features"></a>主要功能

深度相機的技術特性包括：

- 使用先進圖元技術的1百萬 ToF 影像處理晶片，以提供更高的調製頻率和深度精確度。
- 有兩種 NIR 的鐳射二極體，能讓您以近和寬的方式 (FoV) 深度模式。
- 全球最小的 ToF 圖元，3.5 μμ3.5 μ m。
- 每個圖元的自動增益選取範圍，可讓您輕鬆地捕捉近和遠的物件。
- 全球的快門，可改善日光的效能。
- 多階段深度計算方法，即使在晶片、鐳射和電源供應器變化的情況下，也能提供穩固的精確度。
- 系統低系統和隨機錯誤。

![深度模組](./media/concepts/depth-camera-depth-module.jpg)

深度相機會將未經處理的調製 IR 映射傳輸到主機電腦。 在電腦上，GPU 加速深度引擎軟體會將原始信號轉換成深度對應。深度相機支援多種模式。 ** (FoV) 模式的窄欄位**，很適合在 X 和 Y 維度中具有較小範圍的場景，但在 Z 維度中較大的範圍。 如果場景的 X 和 Y 範圍很大，但 Z 範圍較小，則 **寬 FoV 模式** 較適合。

深度相機支援 **2x2 分類收納模式** ，以擴充 Z 範圍，相較于對應的 **包含尚未拋棄模式**。 分類收納是以降低影像解析度的成本來完成。 所有模式最多可以在每秒30個畫面格 (fps) ，但在以最大畫面播放速率（最大畫面播放速率為 15 fps）執行的1張萬圖元 (MP) 模式除外。 深度相機也提供 **被動 IR 模式**。 在此模式中，相機上的 illuminators 不在使用中，而且只會觀察到環境照明。

## <a name="camera-performance"></a>攝影機效能

攝影機的效能會以系統化和隨機錯誤來測量。

### <a name="systematic-error"></a>系統化錯誤

系統會將系統錯誤定義為移除雜訊之後的測量深度，以及正確的 (地面) 深度。 我們會計算靜態場景許多框架的時態平均，盡可能減少深度雜訊。 更精確地說，系統化錯誤的定義如下：

![深度系統化錯誤](./media/concepts/depth-camera-systematic-error.png)

其中*d<sub>t</sub> *代表時間*t*的量值深度， *N*是平均程式中所使用的框架數目，而*d<sub>gt</sub> *是真實的深度。

深度相機的系統化錯誤規格包含 (MPI) 的多重路徑干擾。 MPI 是當某個感應器圖元整合了一個以上的物件所反映的燈光時。 您可以使用較高的調製頻率，在我們的深度相機中部分降低 MPI，以及深度失效（稍後將會推出）。

### <a name="random-error"></a>隨機錯誤

假設我們在不移動攝影機的情況下，取得相同物件的100影像。 在每個100影像中，物件的深度會稍有不同。 這項差異是由拍攝雜訊所造成。 有一段時間的隨機因素，光子觸達感應器的次數會有所不同。 我們會在靜態場景上定義這個隨機錯誤，作為計算時間的深度比計算方式的標準差：

![深度隨機錯誤](./media/concepts/depth-camera-random-error.png)

其中*N*代表深度度量的數目， *d<sub>t</sub> *代表時間的深度量*測，而* *d*表示針對所有深度量測標準計算的平均值*d<sub>t</sub>*。

## <a name="invalidation"></a>失效

在某些情況下，深度相機可能無法為某些圖元提供正確的值。 在這些情況下，深度圖元會失效。 不正確圖元會以等於0的 depth 值表示。 深度引擎無法產生正確值的原因包括：

- 主動 IR 照明遮罩之外
- 飽和 IR 信號
- 低 IR 信號
- 篩選極端
- 多重路徑干擾

### <a name="illumination-mask"></a>照明遮罩

圖元在使用中 IR 照明遮罩之外時會失效。 我們不建議使用這類圖元的信號來計算深度。 下圖顯示依照明遮罩的失效範例。 失效的圖元是寬 FoV 模式中圓形外部的黑色圖元 (左) ，而窄 FoV 模式中的六邊形 (右) 。

![在照明遮罩之外失效](./media/concepts/depth-camera-invalidation-illumination-mask.png)

### <a name="signal-strength"></a>信號強度

圖元如果包含飽和 IR 信號，就會失效。 當圖元飽和時，階段資訊會遺失。 下圖顯示使用飽和 IR 信號的失效範例。 請參閱「深度」和「IR」影像中指向範例圖元的箭號。

![失效飽和度](./media/concepts/depth-camera-invalidation-saturation.png)

當 IR 信號不夠強而無法產生深度時，也會發生失效。 下圖顯示使用低 IR 信號的失效範例。 查看深度和 IR 影像中指向範例圖元的箭號。

![失效的低信號](./media/concepts/depth-camera-invalidation-low-signal.png)

### <a name="ambiguous-depth"></a>不明確的深度

如果圖元從場景中的一個以上的物件收到信號，也可能會失效。 可以看到這類失效的常見案例是在角落。  由於場景幾何的不同，來自相機的紅外線燈會反映在一個牆上。 這種反映的光線會造成圖元的測量深度不明確。 深度演算法中的篩選會偵測這些不明確的信號，並使圖元失效。

下圖顯示透過多重路徑偵測的失效範例。 您也可以查看從某個相機視圖失效的相同介面區域 (前幾個資料列，) 可能會從不同的相機視圖中再次顯示 (下一列) 。 下圖顯示從某個觀點來看，表面可能會從另一個觀點顯示。

![不正確多重路徑-邊角](./media/concepts/depth-camera-invalidation-multipath.png)

多重路徑的另一個常見案例是圖元，其中包含來自前景和背景 (的混合信號，例如圍繞物件邊緣) 。 在快速動作期間，您可能會看到邊緣周圍有更多失效的圖元。 額外的失效圖元是因為原始深度捕捉的曝光間隔，

![不正確多重路徑-邊緣](./media/concepts/depth-camera-invalidation-edge.png)

## <a name="next-steps"></a>後續步驟

[座標系統](coordinate-systems.md)
