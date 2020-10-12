---
title: 將認知服務附加至技能集
titleSuffix: Azure Cognitive Search
description: 將認知服務的多個訂用帳戶附加至 Azure 認知搜尋中 AI 擴充管線的指示。
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: c9f6a5ebc4f3242181196bd40b62f7522d025b84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88924972"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>將認知服務資源附加至 Azure 認知搜尋中的技能集 

在 Azure 認知搜尋中設定擴充管線時，您可以免費擴充有限數量的檔。 針對較大型且較頻繁的工作負載，您應該附加可計費的認知服務資源。

在本文中，您將瞭解如何藉由將金鑰指派給定義擴充管線的技能集來連結資源。

## <a name="resources-used-during-enrichment"></a>擴充期間使用的資源

Azure 認知搜尋相依于認知服務，包括影像分析和光學字元辨識的 [電腦視覺](https://azure.microsoft.com/services/cognitive-services/computer-vision/) (OCR) 、適用于自然語言處理的 [文字分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/) ，以及 [文字轉譯](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)等其他擴充。 在 Azure 認知搜尋的擴充內容中，這些 AI 演算法會包裝在 *技能*內、放置在 *技能集*中，並在索引編制期間由 *索引子* 參考。

## <a name="how-billing-works"></a>計費的運作方式

+ Azure 認知搜尋會使用您在技能集上提供的認知服務資源金鑰來為影像和文字擴充帳單。 計費技能的執行是以 [認知服務隨用隨付價格](https://azure.microsoft.com/pricing/details/cognitive-services/)計費。

+ 「映射解壓縮」是一種 Azure 認知搜尋作業，會在檔在擴充之前進行破解時發生。 影像解壓縮是可計費的。 如需映射解壓縮定價，請參閱 [Azure 認知搜尋定價頁面](https://azure.microsoft.com/pricing/details/search/)。

+ 文字解壓縮也會在檔破解片語期間發生。 不計費。

+ 未呼叫認知服務的技能（包括條件式、整形、文字合併和文字分割技能）不會計費。

## <a name="same-region-requirement"></a>相同區域需求

我們要求 Azure 認知搜尋和 Azure 認知服務都存在於相同的區域中。 否則，您將會在執行時間收到此訊息： `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

沒有任何方法可以跨區域移動服務。 如果您收到此錯誤，您應該在 Azure 認知搜尋的相同區域中建立新的認知服務資源。

> [!NOTE]
> 某些內建技能是根據非區域認知服務 (例如， [文字翻譯技能](cognitive-search-skill-text-translation.md)) 。 使用非區域技能表示您的要求可能會在 Azure 認知搜尋區域以外的區域中提供服務。 如需非區域服務的詳細資訊，請參閱 [依區域的認知服務產品](https://aka.ms/allinoneregioninfo) 頁面。

## <a name="use-free-resources"></a>使用免費資源

您可以使用有限的免費處理選項來完成 AI 擴充教學課程和快速入門練習。

每個索引子每天僅限有20份檔的免費 (擴充) 資源。 您可以刪除並重新建立索引子，以重設計數器。

1. 開啟 [匯入資料] 嚮導：

   ![開啟 [匯入資料] 嚮導](media/search-get-started-portal/import-data-cmd.png "開啟 [匯入資料] 嚮導")

1. 選擇資料來源，然後繼續 **新增 AI 擴充 (選擇性) **。 如需此嚮導的逐步解說，請參閱在 [Azure 入口網站中建立索引](search-get-started-portal.md)。

1. 展開 [ **附加認知服務** ]，然後選取 [ **免費 (有限擴充) **：

   ![展開的附加認知服務區段](./media/cognitive-search-attach-cognitive-services/attach1.png "展開的附加認知服務區段")

1. 您現在可以繼續進行後續步驟，包括 **新增認知技能**。

## <a name="use-billable-resources"></a>使用計費資源

針對每天建立超過20個擴充的工作負載，請務必附加可計費的認知服務資源。 建議您一律附加可計費的認知服務資源，即使您不想要呼叫認知服務 API。 附加資源會覆寫每日限制。

您只需支付呼叫認知服務 API 的技能。 您不需支付 [自訂技能](cognitive-search-create-custom-skill-example.md)的費用，或不是以 API 為基礎的 [文字合併](cognitive-search-skill-textmerger.md)、 [文字分隔器](cognitive-search-skill-textsplit.md)和 [整形](cognitive-search-skill-shaper.md)等技能。

1. 開啟 [匯入資料] 嚮導，選擇資料來源，然後繼續 **新增 AI 擴充 (選擇性的) **。

1. 展開 [ **附加認知服務** ]，然後選取 [ **建立新的認知服務資源**]。 新的索引標籤隨即開啟，讓您可以建立資源：

   ![建立認知服務資源](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "建立認知服務資源")

1. 在 [ **位置** ] 清單中，選取您的 Azure 認知搜尋服務所在的區域。 基於效能考慮，請務必使用此區域。 使用此區域也會在不同區域之間有不一致的輸出頻寬費用。

1. 在 [ **定價層** ] 清單中，選取 [ **S0** ] 以取得「認知服務」功能的全功能集合，包括可取回 Azure 認知搜尋所提供之內建技能的願景和語言功能。

   針對 S0 層，您可以在 [ [認知服務定價] 頁面](https://azure.microsoft.com/pricing/details/cognitive-services/)上找到特定工作負載的費率。
  
   + 在 [ **選取供應** 專案] 清單中，確認已選取 [ **認知服務** ]。
   + 在 [ **語言** 功能] 下， **文字分析標準** 的費率適用于 AI 索引。
   + 在 [ **視覺** 功能] 下，適用 **電腦視覺 S1** 的費率。

1. 選取 [建立] 以布 **建** 新的認知服務資源。

1. 回到上一個索引標籤，其中包含匯入資料的 wizard。 選取 [重新整理] 以顯示認知服務資源， **然後選取資源** ：

   ![選取認知服務資源](./media/cognitive-search-attach-cognitive-services/attach2.png "選取認知服務資源")

1. 展開 [ **新增認知技能** ] 區段，以選取您想要對資料執行的特定認知技能。 完成嚮導的其餘部分。

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>將現有的技能附加至認知服務資源

如果您有現有的技能，就可以將其附加至新的或不同的「認知服務」資源。

1. 在 [ **服務總覽** ] 頁面上，選取 [ **技能集**：

   ![技能集索引標籤](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "技能集索引標籤")

1. 選取技能集的名稱，然後選取現有的資源，或建立一個新的資源。 按一下 [確定]**** 確認變更。

   ![技能集資源清單](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "技能集資源清單")

   請記住， **免費的 (有限擴充) ** 選項會限制您每天20份檔，而且您可以使用 [ **建立新的認知服務資源** ] 來布建新的可計費資源。 如果您建立新資源，請選取 [重新整理]**** 以重新整理「認知服務」資源的清單，然後選取資源。

## <a name="attach-cognitive-services-programmatically"></a>透過程式設計方式附加認知服務

透過程式設計方式定義技能時，請將 `cognitiveServices` 區段新增至該技能。 在該區段中，加入您想要與技能集建立關聯之認知服務資源的金鑰。 請記住，資源必須位於與您的 Azure 認知搜尋資源相同的區域中。 以及包含 `@odata.type`，並將它設定為 `#Microsoft.Azure.Search.CognitiveServicesByKey`。

下列範例示範了此模式。 請注意 `cognitiveServices` 定義結尾的區段。

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
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

若要預估與認知搜尋索引相關的成本，請先瞭解平均檔的外觀，以便您可以執行一些數位。 例如，您可能會近似值：

+ 1000 Pdf。
+ 六個頁面。
+ 每頁一個影像 (6000 個影像) 。
+ 每頁3000個字元。

假設管線包含每個 PDF、影像和文字解壓縮的檔破解、光學字元辨識 (影像的 OCR) ，以及組織的實體識別。

本文所顯示的價格為假設。 它們是用來說明估計程式。 您的成本可能較低。 如需交易的實際價格，請參閱 [認知服務定價](https://azure.microsoft.com/pricing/details/cognitive-services)。

1. 針對文字和影像內容的文件破解，目前文字擷取是免費功能。 針對6000映射，會假設每個已解壓縮的1000影像都是 $1。 這是此步驟的 $6.00 成本。

2. 針對 6,000 個英文影像的 OCR，OCR 認知技能會使用最佳演算法 (DescribeText)。 假設每分析 1,000 個影像的費用為美金 $2.50 元，則在此步驟中您需支付美金 $15.00 元。

3. 針對實體解壓縮，每頁共有三個文字記錄。 每筆記錄是 1,000 個字元。 每頁的三個文字記錄乘以6000頁等於18000個文字記錄。 假設每 1,000 筆文字記錄需要美金 $2.00 元，則此步驟需要美金 $36.00 元。

總而言之，您需要支付 $57.00 的費用，以使用所述的技能集來內嵌此類型的 1000 PDF 檔。

## <a name="next-steps"></a>後續步驟
+ [Azure 認知搜尋定價頁面](https://azure.microsoft.com/pricing/details/search/)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [建立技能集 (REST)](/rest/api/searchservice/create-skillset)
+ [如何對應豐富型欄位](cognitive-search-output-field-mapping.md) (英文)