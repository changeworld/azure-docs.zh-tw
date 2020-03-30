---
title: 使用影片索引器網站自訂品牌模型
titleSuffix: Azure Media Services
description: 瞭解如何使用視頻索引子網站自訂品牌模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 81df3897dff13823e4b97e10bc91d3a22b0e1b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128050"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>使用影片索引器網站自訂品牌模型

影片索引器支援在視訊和音訊內容的編製索引及重新編製索引期間，從語音和視覺文字偵測品牌。 品牌偵測功能可識別 Bing 的品牌資料庫建議所提及的產品、服務以及公司。 例如，如果視頻或音訊內容中提到 Microsoft，或者在視頻中以可視文本顯示，視頻索引子會將其檢測為內容中的品牌。

自訂品牌模型允許您：

- 是否希望視頻索引子從必應品牌資料庫中檢測品牌，請選擇該選擇。
- 如果希望視頻索引子排除檢測到某些品牌（實質上是創建品牌拒絕清單），請選擇該清單。
- 如果您希望視頻索引子包含應該成為您的模型一部分的品牌（基本上創建接受品牌清單），請選擇該品牌。

有關詳細概述，請參閱此[概述](customize-brands-model-overview.md)。

您可以使用影片索引器網站建立、使用和編輯在視訊中偵測到的自訂品牌模型，如本主題中所述。 您也可以使用 API，如[使用 API 自訂品牌模型](customize-brands-model-with-api.md)中所述。

## <a name="edit-brands-model-settings"></a>編輯品牌模型設置

您可以選擇設定是否要從 Bing 品牌資料庫偵測品牌。 要設置此選項，您需要編輯品牌模型的設置。 請遵循下列步驟：

1. 轉到[視頻索引子](https://www.videoindexer.ai/)網站並登錄。
2. 要自訂帳戶中的模型，請選擇頁面右上角**的內容模型自訂**按鈕。

   ![在視頻索引子中自訂內容模型](./media/content-model-customization/content-model-customization.png)

3. 若要編輯品牌，選取 [品牌]**** 索引標籤。

    ![自訂視頻索引子中的品牌模型](./media/customize-brand-model/customize-brand-model.png)

4. 如果您希望視頻索引子檢測必應建議的品牌，請檢查**必應建議的"顯示"品牌**，如果您不希望選中該選項，則不選中該選項。

## <a name="include-brands-in-the-model"></a>包含模型中的品牌

**"包括品牌**"部分表示您希望視頻索引子檢測的自訂品牌，即使必應未建議這些品牌也是如此。  

### <a name="add-a-brand-to-include-list"></a>添加包含清單的品牌

1. 選擇 **= 添加品牌**。

    ![自訂視頻索引子中的品牌模型](./media/customize-brand-model/add-brand.png)

    提供名稱 (必要)、類別 (選擇性)、描述 (選擇性) 以及參考 URL (選擇性)。
    [類別] 欄位是用來協助您標記您的品牌。 此欄位會在使用影片索引器 API 時，顯示為品牌的*標記*。 例如，您可以將品牌 "Azure" 標記或分類為「雲端」。

    參考 URL 欄位可以是該品牌的任何參考網站（如指向其維琪百科頁面的連結）。

2. 選擇 **"添加品牌**"，您將看到該品牌已添加到"**包括品牌**"清單中。

### <a name="edit-a-brand-on-the-include-list"></a>編輯包含清單中的品牌

1. 選擇要編輯的品牌旁邊的鉛筆圖示。

    您可以更新品牌的類別、描述或參考 URL。 您無法變更品牌的名稱，因為品牌的名稱是獨一無二的。 如果您要變更品牌名稱，請刪除整個品牌 (請參閱下一節)，並以新的名稱建立新的品牌。

2. 選擇 **"更新**"按鈕以使用新資訊更新品牌。

### <a name="delete-a-brand-on-the-include-list"></a>刪除包含清單中的品牌

1. 選擇要刪除的品牌旁邊的垃圾回收圖示。
2. 選擇 **"刪除**"，品牌將不再顯示在 *"包括"品牌*清單中。

## <a name="exclude-brands-from-the-model"></a>從模型中排除品牌

**"排除品牌**"部分表示您不希望視頻索引子檢測到的品牌。

### <a name="add-a-brand-to-exclude-list"></a>添加要排除清單的品牌

1. 選擇 **= 添加品牌。**

    提供名稱 (必要)、類別 (選擇性)。

2. 選擇 **"添加品牌**"，您將看到該品牌已添加到 *"排除品牌*"清單中。

### <a name="edit-a-brand-on-the-exclude-list"></a>編輯排除清單中的品牌

1. 選擇要編輯的品牌旁邊的鉛筆圖示。

    您只能更新品牌的類別。 您無法變更品牌的名稱，因為品牌的名稱是獨一無二的。 如果您要變更品牌名稱，請刪除整個品牌 (請參閱下一節)，並以新的名稱建立新的品牌。

2. 選擇 **"更新**"按鈕以使用新資訊更新品牌。

### <a name="delete-a-brand-on-the-exclude-list"></a>刪除排除清單中的品牌

1. 選擇要刪除的品牌旁邊的垃圾回收圖示。
2. 選擇 **"刪除**"，該品牌將不再顯示在 *"排除"品牌*清單中。

## <a name="next-steps"></a>後續步驟

[使用 API 自訂品牌模型](customize-brands-model-with-api.md)
