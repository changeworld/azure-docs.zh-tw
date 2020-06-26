---
title: 在 Maya 中設定以實體為基礎的轉譯材質
description: 說明如何在 Maya 中設定以實體為基礎的轉譯材質，並將其匯出為 FBX 格式
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 5579994b0746a2de4b0f2ca927027ac709940024
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977362"
---
# <a name="tutorial-setting-up-physically-based-rendering-materials-in-maya"></a>教學課程：在 Maya 中設定以實體為基礎的轉譯材質

## <a name="overview"></a>概觀
在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 將具有進階光源模型的材質指派給場景中的物件。
> * 處理物件和材質的執行個體。
> * 將場景匯出為 FBX 格式，以及要選取的重要選項。

在 `Maya` 中建立[以實體為基礎的轉譯 (PBR) 材質](../../overview/features/pbr-materials.md)，是一項相當直接的工作，在許多方面，猶如在其他內容建立應用程式 (如 `3DS Max`) 中設定 PBR 一般。 下列教學課程會指引您如何設定 ARR 著色器，以及以 PBR 匯出 ARR 專案。 

本教學課程中的範例場景包含一些 `Polygon Box` 物件，其已獲指派一些不同的材質 - 木材、金屬、着色金属、塑膠和橡膠。 大致來說，每個材質都包含下列所有或大部分紋理 

* `Albedo` 是材質色階配對，也稱為 `Diffuse` 或 `BaseColor`
* `Metalness`，可判定材質是否為金屬，以及哪些零件是金屬。 
* `Roughness`，可決定表面的粗糙度或平滑度，這會影響表面上反光和高光的清晰度或模糊度。
* `Normals`，可增加表面細節，例如，金屬表面上的點蝕和凹痕，或在木材中的紋理，而不需加入更多的多邊形。
* `Ambient Occlusion`，用來將軟陰影和接觸陰影新增至模型。 其是一個灰階圖，指出模型的哪些區域會收到完整光源 (白色) 或完整陰影 (黑色)。 

## <a name="prerequisites"></a>必要條件
* `Autodesk Maya 2017` 或更新版本

## <a name="setting-up-materials-in-the-scene"></a>在場景中設定材質
在 Maya 中，設定 PBR 材質的程序如下所示：

首先，如您在範例場景中所看到的，我們建立了許多 Box 物件，每個物件代表一種不同類型的材質。 請注意，如下圖所示，已將物件自己適用的名稱提供給每個物件 

> 值得注意的是，在開始建立 Azure 遠端轉譯 (ARR) 的資產之前，其會使用公尺來測量，而向上方向是 Y 軸。 因此，建議您在 Maya 中將場景單位設定為公尺。 此外，建議匯出時，在 [FBX 匯出] 設定中將單位設定為公尺。 

> [!TIP]
最好是適當地命名模型資產，通常使用相關的零件或材質類型，因為名稱將使瀏覽重物場景變得輕鬆。

![物件名稱](media/object-names.jpg)

一旦建立/取得了紋理，您就可以根據自己的需求，在紋理應用程式 (例如 `Quixel Suite`、`Photoshop` 或 `Substance Suite`) 中建立模型的唯一紋理，或者您也可以使用來自其他來源的一般並排紋理，您可以將這些紋理套用至您的模型，如下所示：

* 在您的場景檢視區中，選取您的模型/幾何，並在其上按一下滑鼠右鍵。 在出現的功能表中，按一下 `Assign New Material`
* 在 `Assign New Material` 選項中，移至 `Maya`>`Stingray PBS`。 此動作會將 PBR 材質指派給您的模型。 

在 `Maya 2020` 中，有一些不同的 PBR 著色器可用 - `Maya Standard Surface`、`Arnold Standard Surface` 和 `Stingray PBR`。 `Maya Standard Surface Shader` 尚無法透過 `FBX plugin 2020` 匯出，而 `Arnold Standard Surface Shader` 則可以使用 FBX 檔案匯出。 在其他大部分方面，其與 `Maya Standard Surface Shader` 相同，而且類似於 `3D Studio Max` 中的 `Physical Material`。

**`The Stingray PBR Shader`** 與許多其他應用程式相容，而且最符合 `ARR` 的需求，而且自 `Maya 2017` 以來受到支援。 同樣方便的是，在檢視區中視覺化的這種類型材質類似於稍後在 ARR 中視覺化的材質。

![`Stingray` 材質](media/stingray-material.jpg)

透過將您的材質指派給您的資產並適當地命名，您現在可以繼續指派您的各種紋理。 下圖詳述每種紋理類型符合 PBR 材質的位置。 `Stingray PBR` 材質可讓您選取可啟動的屬性，因此您必須先啟動相關屬性，才能 `plug in` 您的紋理圖： 

![材質設定](media/material-setup.jpg)

> [!TIP]
最好是適當地將您的材質命名，並將其用途和/或類型納入考慮。 要用於唯一零件的材質可能會命名為該零件，而可在更多區域上使用的材質可以命名為其屬性或類型。

指派您的紋理，如下所示：

![紋理設定](media/texture-setup.jpg)

建立並設定您的 PBR 材質後，值得考慮在您的場景中[將物件執行個體化](../../how-tos/conversion/configure-model-conversion.md#instancing)。 將場景中類似的物件 (例如螺母、螺栓、螺絲墊圈) 執行個體化 - 基本上，任何相同的物件都可以大幅節省檔案大小。 主要物件的執行個體可以有自己的縮放、旋轉和轉換，因此可以視需要放置在場景中。 在 Maya 中，執行個體化的程序很簡單。

* 在 `Edit` 功能表中，移至 `Duplicate Special`，然後開啟 `Options`。 
* 在 `Duplicate Special` 選項中，將 `Geometry Type` 從 `Copy` 切換到 `Instance`。 
* 按一下 `Duplicate Special`

![執行個體化](media/instancing.jpg)

這個動作會建立物件的執行個體，而此物件在移動時可以進行旋轉或縮放，與其父系和該父系的其他執行個體無關。 
>不過，在元件模式下對執行個體進行的任何變更都會傳輸到物件的所有執行個體，因此，如果您要使用執行個體物件元件 (頂點、多邊形面等等)，則會先確定您想要任何所做的變更影響所有這些執行個體。

在範例場景中，每個個別的 Box 物件都已執行個體化。 當我們將場景匯出為 FBX 格式時，此動作會有相關性。

![場景概觀](media/scene-overview.jpg)

>關於場景中執行個體化的最佳做法是在進行過程中建立執行個體，因為將「副本」取代為執行個體化的物件非常困難。 

## <a name="fbx-export-process"></a>FBX 匯出程序

我們現在可以繼續透過 FBX 匯出您的場景或場景資產。 一般來說，匯出資產時，從您的場景中只選取您想要的這些物件/資產進行匯出，這是很合理的。 如果您在場景中有 100 個物件，但您只想要使用其中的 30 個，則匯出整個場景沒有意義。 因此，除非您很高興匯出整個場景，否則請進行選擇並移至：

* `File` > `Export Selection`，然後在匯出對話方塊中移至底部，並將 `Files of Type` 設為 `FBX Export`。 此視窗會公開 [FBX 匯出] 設定。 下圖中 [FBX 匯出] 的主要設定會以紅色反白顯示。

![FBX 匯出](media/FBX-exporting.jpg)

根據您的需求 (例如，您可能想要將資產傳送至用戶端，但不想要大量紋理檔案與該資產一起傳送)，您可以選擇在匯出的 FBX 檔案內嵌紋理。 此選項表示您只有一個檔案要封裝，但其會大幅增加該 FBX 資產的大小。 您可以切換 `Embed Media` 選項來啟用內嵌紋理的選項，如下所示。

> [!TIP]
> 請注意，在此情況下，已命名這個檔案來反映此狀況。 這是讓您追蹤資產的好做法。 

一旦您完成了設定組態進行匯出，就請按一下右下方的 [匯出選取項目] 按鈕。

![內嵌媒體](media/embedding-media.jpg)

## <a name="conclusion"></a>結論

通常，這種類型的材質看起來更逼真，因為其根據真實世界的光物理。 其會建立一個額外的沉浸式效果，讓場景存在於現實世界中。

## <a name="next-steps"></a>後續步驟

您現在知道最重要的功能是，讓您將具有進階光源的材質設定為場景中的物件，並將其匯出為 ARR 支援的 FBX 格式。 下一個步驟是轉換 FBX 檔案，並在 ARR 中視覺化。

> [!div class="nextstepaction"]
> [快速入門：轉換模型以進行轉譯](../../quickstarts\convert-model.md)