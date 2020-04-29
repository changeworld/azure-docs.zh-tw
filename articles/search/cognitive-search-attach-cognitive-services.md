---
title: 將認知服務附加至技能集
titleSuffix: Azure Cognitive Search
description: 如何在 Azure 認知搜尋中將認知服務的多個訂用帳戶附加至 AI 擴充管線的指示。
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 254c912114e3f1c7a495f389bc6a6416cbde7e11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77472446"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>將認知服務資源連結至 Azure 認知搜尋中的技能集 

在 Azure 認知搜尋中設定擴充管線時，您可以免費擴充有限數目的檔。 針對較大型且較頻繁的工作負載，您應該附加可計費的認知服務資源。

在本文中，您將瞭解如何藉由將金鑰指派給定義擴充管線的技能集來連結資源。

## <a name="resources-used-during-enrichment"></a>擴充期間使用的資源

Azure 認知搜尋相依于認知服務，包括影像分析和光學字元辨識（OCR）的[電腦視覺](https://azure.microsoft.com/services/cognitive-services/computer-vision/)、自然語言處理的[文字分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)，以及[文字翻譯](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)之類的其他擴充。 在 Azure 認知搜尋的擴充內容中，這些 AI 演算法會包裝在*技能*中，放在*技能集*中，並在索引編制期間參考*索引子*。

## <a name="how-billing-works"></a>計費的運作方式

+ Azure 認知搜尋會使用您在技能集上提供的認知服務資源金鑰來計費影像和文字擴充。 計費技能的執行是認知服務的[隨用隨付價格](https://azure.microsoft.com/pricing/details/cognitive-services/)。

+ 影像解壓縮是一種 Azure 認知搜尋作業，會在擴充之前先解開檔時進行。 影像解壓縮是可計費的。 如需影像解壓縮的價格，請參閱[Azure 認知搜尋定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)。

+ 文字解壓縮也會在檔破解片語期間發生。 這不是可計費的。

+ 不會呼叫認知服務的技能，包括條件式、整形者、文字合併和文字分割技能，都無法計費。

## <a name="same-region-requirement"></a>相同區域需求

我們需要 Azure 認知搜尋和 Azure 認知服務存在於相同的區域內。 否則，您將會在執行時間收到此訊息：`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

沒有任何方法可以跨區域移動服務。 如果您收到此錯誤，您應該在與 Azure 認知搜尋相同的區域中建立新的認知服務資源。

> [!NOTE]
> 某些內建技能是以非區域認知服務為基礎（例如，[文字翻譯技能](cognitive-search-skill-text-translation.md)）。 使用非區域技能表示您的要求可能會在 Azure 認知搜尋區域以外的區域中提供服務。 如需非區域服務的詳細資訊，請參閱[依區域認知服務產品](https://aka.ms/allinoneregioninfo)頁面。

## <a name="use-free-resources"></a>使用免費資源

您可以使用有限的免費處理選項來完成 AI 擴充教學課程和快速入門練習。

每個索引子的免費（有限的擴充）資源限制為每天20份檔。 您可以刪除並重新建立索引子，以重設計數器。

1. 開啟 [匯入資料] 嚮導：

   ![開啟 [匯入資料] 嚮導](media/search-get-started-portal/import-data-cmd.png "開啟 [匯入資料] 嚮導")

1. 選擇資料來源並繼續**新增 AI 擴充（選擇性）**。 如需此 wizard 的逐步解說，請參閱[在 Azure 入口網站中建立索引](search-get-started-portal.md)。

1. 展開 [**附加認知服務**]，然後選取 **[免費（有限的擴充）**]：

   ![展開的附加認知服務區段](./media/cognitive-search-attach-cognitive-services/attach1.png "展開的附加認知服務區段")

1. 您現在可以繼續進行後續步驟，包括**新增認知技能**。

## <a name="use-billable-resources"></a>使用計費資源

針對每天建立超過20個擴充的工作負載，請務必附加可計費的認知服務資源。 我們建議您一律附加可計費的認知服務資源，即使您不想要呼叫認知服務 API 也一樣。 附加資源會覆寫每日限制。

您只需支付呼叫認知服務 API 的技能。 您不需支付[自訂技能](cognitive-search-create-custom-skill-example.md)或[文字合併](cognitive-search-skill-textmerger.md)、[文字分隔器](cognitive-search-skill-textsplit.md)和[整形](cognitive-search-skill-shaper.md)程式等技能，這不是以 API 為基礎。

1. 開啟 [匯入資料] 嚮導，選擇 [資料來源]，然後繼續**新增 AI 擴充（選擇性）**。

1. 展開 [**附加認知服務**]，然後選取 [**建立新的認知服務資源**]。 隨即會開啟新的索引標籤，讓您可以建立資源：

   ![建立認知服務資源](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "建立認知服務資源")

1. 在 [**位置**] 清單中，選取您的 Azure 認知搜尋服務所在的區域。 基於效能考慮，請務必使用此區域。 使用此區域也會對區域之間的輸出頻寬費用產生不一致的情況。

1. 在 [**定價層**] 清單中，選取 [ **S0** ] 以取得認知服務功能的多功能集合，包括可回到 Azure 認知搜尋所提供之內建技能的願景和語言功能。

   針對 S0 層，您可以在[認知服務定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/)上找到特定工作負載的費率。
  
   + 在 [**選取供應**專案] 清單中，確認已選取 [**認知服務**]。
   + 在 [**語言**功能] 底下，**文字分析 Standard**的費率適用于 AI 索引。
   + 在 [**視覺**功能] 底下，適用**電腦視覺 S1**的費率。

1. 選取 [建立] 以布**建**新的認知服務資源。

1. 返回上一個索引標籤，其中包含 [匯入資料] wizard。 選取 [重新整理] 以顯示認知服務資源，**然後選取資源**：

   ![選取認知服務資源](./media/cognitive-search-attach-cognitive-services/attach2.png "選取認知服務資源")

1. 展開 [**新增認知技能**] 區段，以選取您想要在資料上執行的特定認知技能。 完成嚮導的其餘部分。

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>將現有的技能附加至認知服務資源

如果您有現有的技能，就可以將其附加至新的或不同的「認知服務」資源。

1. 在 [**服務總覽**] 頁面上，選取 [**技能集**]：

   ![技能集索引標籤](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "技能集索引標籤")

1. 選取技能集的名稱，然後選取現有的資源或建立一個新的資源。 按一下 [確定]**** 確認變更。

   ![技能集資源清單](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "技能集資源清單")

   請記住，[**免費（有限的擴充）** ] 選項會每天限制您20份檔，而且您可以使用 [**建立新的認知服務資源**] 來布建新的可計費資源。 如果您建立新資源，請選取 [重新整理]**** 以重新整理「認知服務」資源的清單，然後選取資源。

## <a name="attach-cognitive-services-programmatically"></a>透過程式設計方式附加認知服務

透過程式設計方式定義技能時，請將 `cognitiveServices` 區段新增至該技能。 在該區段中，包含您要與技能集建立關聯之認知服務資源的索引鍵。 請記住，資源必須與您的 Azure 認知搜尋資源位於相同的區域。 以及包含 `@odata.type`，並將它設定為 `#Microsoft.Azure.Search.CognitiveServicesByKey`。

下列範例示範了此模式。 請注意`cognitiveServices`定義結尾的區段。

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>範例：預估成本

若要估計與認知搜尋索引相關的成本，請從一般檔的外觀開始著手，讓您可以執行一些數位。 例如，您可能會大致估計：

+ 1000 Pdf。
+ 每個頁面都有六頁。
+ 每頁一個影像（6000個影像）。
+ 每頁3000個字元。

假設管線包含每個 PDF 的檔破解、影像和文字解壓縮、影像的光學字元辨識（OCR），以及組織的實體辨識。

本文中所顯示的價格是假設的。 它們是用來說明估計程式。 您的成本可能較低。 如需交易的實際價格，請參閱[認知服務定價](https://azure.microsoft.com/pricing/details/cognitive-services)。

1. 針對文字和影像內容的文件破解，目前文字擷取是免費功能。 針對6000映射，會針對每個已解壓縮的1000影像假設 $1。 這是此步驟的 $6.00 成本。

2. 針對 6,000 個英文影像的 OCR，OCR 認知技能會使用最佳演算法 (DescribeText)。 假設每分析 1,000 個影像的費用為美金 $2.50 元，則在此步驟中您需支付美金 $15.00 元。

3. 針對實體解壓縮，每頁總共會有三個文字記錄。 每筆記錄是 1,000 個字元。 每個頁面乘以6000頁的三個文字記錄等於18000文字記錄。 假設每 1,000 筆文字記錄需要美金 $2.00 元，則此步驟需要美金 $36.00 元。

總之，您需要支付 $57.00，才能使用所述的技能集來內嵌這種類型的 1000 PDF 檔。

## <a name="next-steps"></a>後續步驟
+ [Azure 認知搜尋定價頁面](https://azure.microsoft.com/pricing/details/search/)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [建立技能集 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何對應豐富型欄位](cognitive-search-output-field-mapping.md) (英文)
