---
title: 使用影片索引器網站自訂品牌模型
titleSuffix: Azure Media Services
description: 瞭解如何使用影片索引子網站自訂品牌模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 5bd88493324867dc957922a732506f5cfb8bbc20
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361229"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>使用影片索引器網站自訂品牌模型

影片索引器支援在視訊和音訊內容的編製索引及重新編製索引期間，從語音和視覺文字偵測品牌。 品牌偵測功能可識別 Bing 的品牌資料庫建議所提及的產品、服務以及公司。 例如，如果影片或音訊內容中提及 Microsoft，或在影片中顯示視覺文字，則影片索引子會將其偵測為內容中的品牌。

自訂品牌模型可讓您：

- 如果您希望影片索引子偵測 Bing 品牌資料庫的品牌，請選取此選擇。
- 如果您想要讓影片索引子排除偵測到的特定品牌 (基本上是建立) 的拒絕清單，請選取此項。
- 如果您想要讓影片索引子包含可能不在 Bing 品牌資料庫中之模型的品牌， (基本上是建立) 品牌的接受清單。

如需詳細的總覽，請參閱此 [總覽](customize-brands-model-overview.md)。

您可以使用影片索引器網站建立、使用和編輯在視訊中偵測到的自訂品牌模型，如本主題中所述。 您也可以使用 API，如[使用 API 自訂品牌模型](customize-brands-model-with-api.md)中所述。

## <a name="edit-brands-model-settings"></a>編輯品牌模型設定

您可以選擇設定是否要從 Bing 品牌資料庫偵測品牌。 若要設定此選項，您必須編輯品牌模型的設定。 請遵循下列步驟：

1. 移至 [影片索引子](https://www.videoindexer.ai/) 網站並登入。
2. 若要在您的帳戶中自訂模型，請選取頁面右上角的 [ **內容模型自訂** ] 按鈕。

   ![在影片索引子中自訂內容模型](./media/content-model-customization/content-model-customization.png)

3. 若要編輯品牌，選取 [品牌]**** 索引標籤。

    ![螢幕擷取畫面：顯示 [內容模型自訂] 對話方塊的 [品牌] 索引標籤。](./media/customize-brand-model/customize-brand-model.png)

4. 如果您希望影片索引子偵測 Bing 建議的品牌，請核取 [ **顯示 bing 建議的品牌** ] 選項，如果您沒有，請將此選項保留為未核取。

## <a name="include-brands-in-the-model"></a>包含模型中的品牌

[ **包含品牌** ] 區段代表您希望影片索引子偵測的自訂品牌，即使它們不是 Bing 的建議。  

### <a name="add-a-brand-to-include-list"></a>新增品牌以包含清單

1. 選取 [ **+ 新增品牌**]。

    ![螢幕擷取畫面顯示 [包含品牌] 對話方塊，您可以在其中新增品牌。](./media/customize-brand-model/add-brand.png)

    提供名稱 (必要)、類別 (選擇性)、描述 (選擇性) 以及參考 URL (選擇性)。
    [類別] 欄位是用來協助您標記您的品牌。 此欄位會在使用影片索引器 API 時，顯示為品牌的*標記*。 例如，您可以將品牌 "Azure" 標記或分類為「雲端」。

    參考 URL 欄位可以是品牌 (的任何參考網站，例如其維琪百科頁面) 的連結。

2. 選取 [ **新增品牌** ]，您將會看到該品牌已新增至 [ **包含品牌** ] 清單。

### <a name="edit-a-brand-on-the-include-list"></a>編輯包含清單上的品牌

1. 選取您要編輯之品牌旁的鉛筆圖示。

    您可以更新品牌的類別、描述或參考 URL。 您無法變更品牌的名稱，因為品牌的名稱是獨一無二的。 如果您要變更品牌名稱，請刪除整個品牌 (請參閱下一節)，並以新的名稱建立新的品牌。

2. 選取 [ **更新** ] 按鈕，以新資訊更新品牌。

### <a name="delete-a-brand-on-the-include-list"></a>刪除包含清單上的品牌

1. 選取您要刪除之品牌旁的垃圾桶圖示。
2. 選取 [ **刪除** ]，品牌將不會再出現在 [ *包含品牌* ] 清單中。

## <a name="exclude-brands-from-the-model"></a>從模型中排除品牌

[ **排除品牌** ] 區段代表您不希望影片索引子偵測的品牌。

### <a name="add-a-brand-to-exclude-list"></a>新增要排除清單的品牌

1. 選取 [ **+ 新增品牌]。**

    提供名稱 (必要)、類別 (選擇性)。

2. 選取 [ **新增品牌** ]，您將會看到該品牌已新增至 [ *排除品牌* ] 清單。

### <a name="edit-a-brand-on-the-exclude-list"></a>編輯排除清單上的品牌

1. 選取您要編輯之品牌旁的鉛筆圖示。

    您只能更新品牌的類別。 您無法變更品牌的名稱，因為品牌的名稱是獨一無二的。 如果您要變更品牌名稱，請刪除整個品牌 (請參閱下一節)，並以新的名稱建立新的品牌。

2. 選取 [ **更新** ] 按鈕，以新資訊更新品牌。

### <a name="delete-a-brand-on-the-exclude-list"></a>刪除排除清單上的品牌

1. 選取您要刪除之品牌旁的垃圾桶圖示。
2. 選取 [ **刪除** ]，品牌將不會再出現在 *排除品牌* 清單中。

## <a name="next-steps"></a>後續步驟

[使用 API 自訂品牌模型](customize-brands-model-with-api.md)
