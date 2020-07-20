---
title: Azure Kinect DK 深度攝影機
description: 瞭解您 Azure Kinect DK 中深度攝影機的操作原則和主要功能。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect，azure，感應器，sdk，深度攝影機，tof，原則，效能，失效
ms.openlocfilehash: 22f04b983ed7c6a2ab19a5c1c709621655ee31c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276486"
---
# <a name="azure-kinect-dk-depth-camera"></a>Azure Kinect DK 深度攝影機

本頁涵蓋如何使用 Azure Kinect DK 中的深度相機。 深度相機是兩個相機的第二個。 如先前各節所述，另一個攝影機是 RGB 攝影機。  

## <a name="operating-principles"></a>操作原則

Azure Kinect DK 深度攝影機會執行「振幅」連續 Wave （AMCW）「時間（ToF）」原則。 相機會將近紅外線（NIR）頻譜中的調製照明轉換成場景。 然後，它會記錄光線從相機到場景來回移動所需的間接測量時間。

系統會處理這些測量，以產生深度對應。 深度對應是影像中每個圖元的一組 Z 座標值，以毫米為單位。

除了深度對應之外，我們也會取得所謂的乾淨 IR 閱讀。 全新 IR 讀取中的圖元值，與場景所傳回的光線量成正比。 影像看起來類似一般 IR 影像。 下圖顯示範例深度對應（左）和對應的全新 IR 影像（right）。

![並排的深度和 IR](./media/concepts/depth-camera-depth-ir.png)

## <a name="key-features"></a>主要功能

深度攝影機的技術特性包括：

- 1-百萬 ToF 影像處理晶片，具備先進的圖元技術，讓您能夠提供更高的調製頻率和深度精確度。
- 兩個 NIR 鐳射二極體，能啟用接近且寬的現場視圖（FoV）深度模式。
- 全球最小的 ToF 圖元，位於3.5 μ乘以3.5 μ m。
- 自動每圖元增益選取範圍可讓您大幅地捕捉近乎和遠處的物件。
- 全域快門，允許在陽光中改善效能。
- 多階段深度計算方法，即使在晶片、鐳射和電源供應器變化的情況下，也能提供健全的精確度。
- 低系統化和隨機錯誤。

![深度模組](./media/concepts/depth-camera-depth-module.jpg)

深度相機會將未經處理的調製 IR 影像傳輸到主機電腦。 在電腦上，GPU 加速深度引擎軟體會將原始信號轉換成深度對應。深度攝影機支援數種模式。 [**縮小] 欄位（FoV）** 模式非常適合在 X 和 Y 維度中具有較小範圍的場景，但 Z 維度中的範圍較大。 如果場景具有大型 X 和 Y 範圍，但 Z 範圍較小，則**寬 FoV 模式**較適合。

深度相機支援以**2x2 分類收納模式**來擴充 Z 範圍，相較于對應的**包含尚未拋棄模式**。 分類收納是以降低映射解析度的成本來完成。 所有模式最多可以執行每秒30個畫面格數（fps），但以最大畫面播放速率 15 fps 來執行的1個百萬（MP）模式除外。 深度攝影機也提供**被動 IR 模式**。 在此模式中，相機上的 illuminators 不是作用中，而且只會觀察到環境照明。

## <a name="camera-performance"></a>相機效能

相機的效能會以系統化和隨機錯誤來測量。

### <a name="systematic-error"></a>系統化錯誤

系統會將系統化錯誤定義為移除雜訊後的測量深度與正確（真）深度之間的差異。 我們會計算靜態場景的許多框架的時態平均值，以盡可能排除深度雜訊。 更精確地說，系統化錯誤的定義如下：

![深度系統化錯誤](./media/concepts/depth-camera-systematic-error.png)

其中*d<sub>t</sub> *代表時間*t*的量值深度， *N*是平均程式中使用的框架數目，而*d<sub>gt</sub> *則是一種基本的深度。

深度相機的系統化錯誤規格不包括多重路徑干擾（MPI）。 MPI 是當一個感應器圖元整合了一個由多個物件所反映的光線時。 在我們的深度攝影機中，使用較高的調製頻率來部分緩和 MPI，並附上深度失效，我們將在稍後介紹。

### <a name="random-error"></a>隨機錯誤

假設我們採用相同物件的100影像，而不移動相機。 在每個100影像中，物件的深度會稍有不同。 這項差異是由拍攝的雜訊所造成。 「拍攝雜訊」是達到感應器的光子數目會因一段時間的隨機因素而有所不同。 我們會在靜態場景上定義此隨機錯誤，做為計算為的一段時間之深度標準差：

![深度隨機錯誤](./media/concepts/depth-camera-random-error.png)

其中*N*表示深度測量的數目， *d<sub>t</sub> *代表時間*t*的深度量值，而*d*則表示計算所有深度測量*d<sub>t</sub>* 的平均值。

## <a name="invalidation"></a>無效

在某些情況下，深度攝影機可能無法為某些圖元提供正確的值。 在這些情況下，深度圖元會無效。 無效圖元以 [深度] 值等於0表示。 深度引擎無法產生正確值的原因包括：

- 使用中 IR 照明遮罩以外的範圍
- 飽和 IR 信號
- 低 IR 信號
- 篩選準則極端
- 多重路徑干擾

### <a name="illumination-mask"></a>照明遮罩

當圖元位於作用中 IR 照明遮罩之外時，會使其失效。 我們不建議使用這類圖元的信號來計算深度。 下圖顯示依照明遮罩的失效範例。 失效圖元是寬 FoV 模式（左）中圓形外的黑色圖元，而 FoV 模式中的六邊形（right）。

![在照明遮罩外失效](./media/concepts/depth-camera-invalidation-illumination-mask.png)

### <a name="signal-strength"></a>信號強度

當圖元包含飽和 IR 信號時，會使其失效。 當圖元飽和時，會遺失階段資訊。 下圖顯示透過飽和 IR 信號來失效的範例。 請參閱指向深度和 IR 影像中的範例圖元的箭號。

![失效飽和度](./media/concepts/depth-camera-invalidation-saturation.png)

當 IR 信號不夠強而無法產生深度時，也可能會發生失效。 下圖顯示的是低 IR 信號的失效範例。 請參閱指向深度和 IR 影像中的範例圖元的箭號。

![失效的低信號](./media/concepts/depth-camera-invalidation-low-signal.png)

### <a name="ambiguous-depth"></a>不明確的深度

如果圖元從場景中的多個物件收到信號，也可能會使其失效。 可以看到這類失效的常見情況是在角落。  由於場景幾何的不同，來自相機的 IR 光線會反映在一個牆上，另一個則會。 這個反映的光源會導致圖元的測量深度不明確。 深度演算法中的篩選會偵測到這些不明確的信號，並使圖元失效。

下圖顯示透過多重路徑偵測而失效的範例。 您也可以看到從一個相機視圖（頂端列）失效的相同介面區，可能會從不同的相機視圖（底端列）重新出現。 此圖顯示從一個觀點來看，表面可能會從另一個觀點來看。

![失效多重路徑-角落](./media/concepts/depth-camera-invalidation-multipath.png)

多重路徑的另一個常見案例是包含來自前景和背景之混合信號的圖元（例如物件邊緣周圍）。 在快速動作期間，您可能會在邊緣周圍看到更多失效的圖元。 額外的失效圖元是因為原始深度捕捉的曝光間隔，

![失效多重路徑邊緣](./media/concepts/depth-camera-invalidation-edge.png)

## <a name="next-steps"></a>後續步驟

[座標系統](coordinate-systems.md)
