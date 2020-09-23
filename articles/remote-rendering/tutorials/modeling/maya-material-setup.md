---
title: 在 Maya 中設定以實體為基礎的轉譯材質
description: 說明如何在 Maya 中設定以實體為基礎的轉譯材質，並將其匯出為 FBX 格式。
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 43c11575be084354e4c9287b44b5847d4d086aff
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90084391"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-maya"></a>教學課程：在 Maya 中設定以實體為基礎的轉譯材質

## <a name="overview"></a>概觀
在本教學課程中，您將了解如何：

> [!div class="checklist"]
>
> * 將具有進階光源的材質指派給場景中的物件。
> * 處理物件和材質的執行個體。
> * 將場景匯出為 FBX 格式，並選取的重要選項。

在 Maya 中建立[以實體為基礎的轉譯 (PBR) 材質](../../overview/features/pbr-materials.md)，這是相當簡單的工作。 在許多方式中，這類似於在其他內容建立應用程式 (例如 3DS Max) 中進行 PBR 設定。 本教學課程會指引您如何設定 PBR 著色器，以及以 FBX 匯出 Azure 遠端轉譯專案。 

本教學課程中的範例場景包含多個多邊形方塊物件。 這些專案會獲指派不同的材質，例如木材、金屬、繪製金屬、塑膠和橡膠。 大致來說，每個材質都包含下列所有或大部分紋理：

* **Albedo** 是材質的色階配對，也稱為 **Diffuse** 或 **BaseColor**。
* **Metalness** 可判定材質是否為金屬，以及哪些零件是金屬。 
* **粗糙度**，可決定表面的粗糙度或平滑度，這會影響表面上反光和高光的清晰度或模糊度。
* **一般**，這會將詳細資料新增至介面，而不需要新增更多多邊形。 詳細資料範例可能是金屬表面上的凹陷和凹痕，或木材中的顆粒。
* **環境遮蔽**，用來將軟陰影和接觸陰影新增至模型。 這是一個灰階圖，指出模型的哪些區域會收到完整光源 (白色) 或完整陰影 (黑色)。 

## <a name="prerequisites"></a>必要條件
* Autodesk Maya 2017 或更新版本

## <a name="set-up-materials-in-the-scene"></a>在場景中設定材質
以下說明如何在 Maya 中設定 PBR 材質。

如您在範例場景中所見，我們建立了一些方塊物件。 每個物件都代表不同類型的材質。 如影像所示，已提供每個物件適用的名稱。

Azure 遠端轉譯會使用計量來進行測量，而向上方向是 Y 軸。 開始建立資產之前，建議您將 Maya 中的場景單位設定為計量。 若要匯出，請在 FBX 匯出設定中將單位設定為計量。

> [!TIP]
> 根據相關的元件或材質類型，為您的模型資產提供適當的名稱。 有意義的名稱可讓您更輕鬆地瀏覽物件較多的場景。

![物件名稱](media/object-names.jpg)

建立或取得某個紋理之後，您也可以建立唯一的紋理。 您可以使用 Quixel Suite、PhotoShop 或 Substance Suite 之類的紋理應用程式，或從其他來源取得通用的並排平紋理。

若要將材質套用至您的模型：

1. 在您的場景檢視區中，選取您的模型或幾何，並在其上按一下滑鼠右鍵。 在出現的功能表中，選取 [指派新的材質]。
1. 在 [指派新的材質] 對話方塊中，移至 **Maya** > **Stingray PBS**。 此動作會將 PBR 材質指派給您的模型。 

在 Maya 2020 中，有一些不同的 PBR 著色器可用。 其中包括 **Maya Standard Surface**、**Arnold Standard Surface** 和 **tingray PBR**。 **Maya Standard Surface Shader** 尚未透過 FBX 2020 外掛程式匯出。 **Arnold Standard Surface Shader** 可以使用 FBX 檔匯出。 在大部分的情況下，其與 **Maya Standard Surface Shader** 相同。 這類似于 3D Studio Max 中的**實體材質**。

**Stingray PBR Shader** 與許多其他應用程式相容，而且最符合 Azure 遠端轉譯的需求， 且從 Maya 2017 開始支援。 當此類型的材質在檢視區中進行視覺化時，會類似於稍後在 Azure 遠端轉譯中進行視覺化的內容。

![Stingray 材質](media/stingray-material.jpg)

透過將您的材質指派給您的資產並適當地命名，您現在可以指派您的各種紋理。 下圖顯示每種紋理類型符合 PBR 材質的位置。 Stingray PBR 材質可讓您選取可啟動的屬性。 插入材質對應之前，您必須先啟動相關的屬性。

![材質設定](media/material-setup.jpg)

適當地將材質命名，將其用途和/或類型納入命名考量。 在唯一組件上使用的材質可能依該組件命名。 在範圍較廣的區域上使用的材質可以用其屬性或類型命名。

指派您的紋理，如影像中所示。

![紋理設定](media/texture-setup.jpg)

建立並設定您的 PBR 材質後，請考慮在您的場景中[將物件執行個體化](../../how-tos/conversion/configure-model-conversion.md#instancing)。 將場景中的類似物件執行個體化，例如螺母、螺栓、螺絲和墊圈，可大幅節省檔案大小。 主要物件的執行個體可以有自己的縮放、旋轉和轉換，因此可以視需要放置在場景中。 

在 Maya 中，執行個體化的程序很簡單。

1. 在 [編輯] 功能表上，移至 [重複特殊項目] 以開啟選項。
1. 在 [重複特殊選項] 對話方塊中，針對 [幾何類型] 選取 [執行個體] 選項。 
1. 選取 [重複特殊項目]。

   ![執行個體化](media/instancing.jpg)

此動作會建立物件的執行個體。 您可以單獨移動、旋轉或調整其父系和該父系的其他執行個體。 

執行個體在元件模式中所做的任何變更，都會傳送至物件的所有執行個體。 例如，您可以使用執行個體物件的元件，例如頂點和多邊形臉部。 請確定您希望所進行的任何變更可以影響所有這些執行個體。 

在範例場景中，每個個別的方塊物件都已執行個體化。 當我們將場景匯出為 FBX 格式時，此動作會有相關性。

![場景概觀](media/scene-overview.jpg)

> [!TIP]
> 當您繼續時，在場景中建立執行個體。 之後要以執行個體物件取代複本會非常困難。 

## <a name="fbx-export-process"></a>FBX 匯出程序

我們現在繼續透過 FBX 匯出您的場景或場景資產。 當您匯出資產時，只需要從您要匯出的場景選取物件或資產。 例如，您在場景中可能會有 100 物件。 如果您只要使用其中的 30 個，則匯出整個場景並沒有幫助。 

透過以下方式加以選取：

1. 移至**檔案** > **匯出選取項目** 以開啟 [匯出選取項目] 對話方塊。
1. 在 [類型檔案] 方塊中，選取 [FBX 匯出] 以顯示 FBX 匯出設定。 圖中 FBX 匯出的主要設定會以紅色反白顯示。

   ![FBX 匯出](media/FBX-exporting.jpg)

例如，視您的需求而定，建議您將資產傳送至用戶端。 也許您不想要傳送有大量材質檔案的資產。 您可以選擇在匯出的 FBX 檔案中內嵌紋理。 此選項表示您只有一個檔案要封裝，但會大幅增加該 FBX 資產的大小。 您可以選取**內嵌媒體**選項來啟用內嵌紋理的選項，如下所示。

> [!TIP]
> 在此範例中，已命名檔案以反映這種情況。 此命名樣式是追蹤資產的好方法。 

設定匯出的組態後，請選取右下方的 [匯出選取項目]。

![內嵌媒體](media/embedding-media.jpg)

## <a name="conclusion"></a>結論

通常，這種類型的材質看起來更逼真，因為這是以真實世界的光線物理學為依據。 其會建立一個額外的沉浸式效果，讓場景好像存在於現實世界中。

## <a name="next-steps"></a>後續步驟

您現在已經知道如何為場景中的物件設定具有進階光源的材質。 也知道如何將物件匯出為 Azure 遠端轉譯所支援的 FBX 格式。 下一個步驟是轉換 FBX 檔案，並在 Azure 遠端轉譯呈現中將其視覺化。

> [!div class="nextstepaction"]
> [快速入門：轉換模型以進行轉譯](../../quickstarts\convert-model.md)