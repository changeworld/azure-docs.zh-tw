---
title: 將認知服務附加到技能集
titleSuffix: Azure Cognitive Search
description: 有關在 Azure 認知搜索中將認知服務一體式訂閱附加到 AI 擴充管道的說明。
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 254c912114e3f1c7a495f389bc6a6416cbde7e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472446"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>將認知服務資源附加到 Azure 認知搜索中的技能集 

在 Azure 認知搜索中配置濃縮管道時，可以免費豐富數量有限的文檔。 對於更大和更頻繁的工作負載，應附加計費的認知服務資源。

在本文中，您將學習如何通過將金鑰分配給定義濃縮管道的技能集來附加資源。

## <a name="resources-used-during-enrichment"></a>濃縮期間使用的資源

Azure 認知搜索依賴于認知服務，包括用於圖像分析和光學字元辨識 （OCR）[的電腦視覺](https://azure.microsoft.com/services/cognitive-services/computer-vision/)、用於自然語言處理[的文本分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)以及其他豐富功能，如[文本翻譯](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)。 在 Azure 認知搜索中的擴充上下文中，這些 AI 演算法被包裝在*技能*中，放置在*技能集中**，並在索引*器編制期間引用。

## <a name="how-billing-works"></a>計費的運作方式

+ Azure 認知搜索使用您在技能集上提供的認知服務資源金鑰，以便為圖像和文本富集計費。 執行計費技能是在[認知服務支付即用即付的價格](https://azure.microsoft.com/pricing/details/cognitive-services/)。

+ 圖像提取是 Azure 認知搜索操作，在富集之前文檔破裂時發生。 圖像提取是計費的。 有關圖像提取定價，請參閱[Azure 認知搜索定價頁](https://go.microsoft.com/fwlink/?linkid=2042400)。

+ 文本提取也會在文檔破解短語期間發生。 它不計費。

+ 不調用認知服務的技能（包括條件、沙珀、文本合併和文本拆分技能）不可計費。

## <a name="same-region-requirement"></a>同一區域要求

我們要求 Azure 認知搜索和 Azure 認知服務存在於同一區域中。 否則，您將在運行時收到此消息：`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

無法跨區域移動服務。 如果收到此錯誤，則應在 Azure 認知搜索相同的區域中創建新的認知服務資源。

> [!NOTE]
> 某些內置技能基於非區域認知服務（例如，[文本翻譯技能](cognitive-search-skill-text-translation.md)）。 使用非區域技能意味著您的請求可能在 Azure 認知搜索區域以外的區域中提供服務。 有關詳細資訊，請參閱[認知服務產品（按區域）](https://aka.ms/allinoneregioninfo)頁面。

## <a name="use-free-resources"></a>使用免費資源

您可以使用有限的免費處理選項來完成 AI 擴充教程和快速入門練習。

每個索引子每天只能提供 20 個文檔的免費（有限濃縮）資源。 您可以刪除並重新創建索引子以重置計數器。

1. 打開導入資料嚮導：

   ![打開導入資料嚮導](media/search-get-started-portal/import-data-cmd.png "打開導入資料嚮導")

1. 選擇資料來源並繼續**添加 AI 擴充（可選）。** 有關此嚮導的分步演練，請參閱[在 Azure 門戶中創建索引](search-get-started-portal.md)。

1. 展開**附加認知服務**，然後選擇**免費（有限濃縮）：**

   ![擴展附加認知服務部分](./media/cognitive-search-attach-cognitive-services/attach1.png "擴展附加認知服務部分")

1. 現在，您可以繼續執行後續步驟，包括**添加認知技能**。

## <a name="use-billable-resources"></a>使用計費資源

對於每天創建 20 次以上富集的工作負載，請確保附加計費的認知服務資源。 我們建議您始終附加計費的認知服務資源，即使您從未打算調用認知服務 API 也是如此。 附加資源將覆蓋每日限制。

您只對稱為認知服務 API 的技能收費。 您不需要按[自訂技能](cognitive-search-create-custom-skill-example.md)收費，或者像[文本合併](cognitive-search-skill-textmerger.md)、[文本拆分器](cognitive-search-skill-textsplit.md)和[shaper](cognitive-search-skill-shaper.md)這樣的技能，這些技能不是基於 API 的。

1. 打開導入資料嚮導，選擇資料來源，並繼續**添加 AI 充實（可選）。**

1. 展開**附加認知服務**，然後選擇**創建新的認知服務資源**。 將打開一個新選項卡，以便您可以創建資源：

   ![建立認知服務資源](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "建立認知服務資源")

1. 在 **"位置**"清單中，選擇 Azure 認知搜索服務所在的區域。 出於性能原因，請確保使用此區域。 使用此區域也會使跨區域的出站頻寬費用失效。

1. 在**定價層**清單中，選擇**S0**以獲取認知服務功能的一體集合，包括支援 Azure 認知搜索提供的內置技能的視覺和語言功能。

   對於 S0 層，您可以在[認知服務定價頁上](https://azure.microsoft.com/pricing/details/cognitive-services/)找到特定工作負載的費率。
  
   + 在 **"選擇產品/服務**"清單中，請確保選擇了**認知服務**。
   + 在**語言**功能下，**文本分析標準**的比率適用于 AI 索引。
   + 在**視覺**功能下，**應用電腦視覺 S1**的費率。

1. 選擇 **"創建**"以預配新的認知服務資源。

1. 返回到包含"導入資料嚮導"的上一個選項卡。 選擇 **"刷新**"以顯示認知服務資源，然後選擇資源：

   ![選擇認知服務資源](./media/cognitive-search-attach-cognitive-services/attach2.png "選擇認知服務資源")

1. 展開"**添加認知技能"** 部分，以選擇要在資料上運行的特定認知技能。 完成嚮導的其餘部分。

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>將現有的技能附加至認知服務資源

如果您有現有的技能，就可以將其附加至新的或不同的「認知服務」資源。

1. 在 **"服務概述"** 頁上，選擇**技能集**：

   ![技能集選項卡](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "技能集選項卡")

1. 選擇技能集的名稱，然後選擇現有資源或創建新資源。 按一下 [確定]**** 確認變更。

   ![技能集資源清單](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "技能集資源清單")

   請記住，"**免費（有限濃縮）"** 選項每天限制您使用 20 個文檔，並且您可以使用 **"創建新的認知服務"資源**來預配新的計費資源。 如果您建立新資源，請選取 [重新整理]**** 以重新整理「認知服務」資源的清單，然後選取資源。

## <a name="attach-cognitive-services-programmatically"></a>透過程式設計方式附加認知服務

透過程式設計方式定義技能時，請將 `cognitiveServices` 區段新增至該技能。 在本節中，包括要與技能集關聯的認知服務資源的鍵。 請記住，資源必須與 Azure 認知搜索資源位於同一區域。 以及包含 `@odata.type`，並將它設定為 `#Microsoft.Azure.Search.CognitiveServicesByKey`。

下列範例示範了此模式。 請注意定義`cognitiveServices`末尾的節。

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

## <a name="example-estimate-costs"></a>示例：估算成本

要估計與認知搜索索引相關的成本，首先瞭解平均文檔的外觀，以便您可以運行一些數位。 例如，您可能近似于：

+ 1，000 PDF。
+ 每頁六頁。
+ 每頁一張圖像（6，000 張圖像）。
+ 每頁 3，000 個字元。

假設一個管道，該管道包括每個 PDF 的文檔破解、圖像和文本提取、圖像的光學字元辨識 （OCR） 以及組織的實體識別。

本文所示的價格是假設的。 它們用於說明估計過程。 您的成本可能會更低。 有關交易的實際價格，請參閱[認知服務定價](https://azure.microsoft.com/pricing/details/cognitive-services)。

1. 針對文字和影像內容的文件破解，目前文字擷取是免費功能。 對於 6，000 個圖像，假設每提取 1，000 張圖像 1 美元。 此步驟的成本為 6.00 美元。

2. 針對 6,000 個英文影像的 OCR，OCR 認知技能會使用最佳演算法 (DescribeText)。 假設每分析 1,000 個影像的費用為美金 $2.50 元，則在此步驟中您需支付美金 $15.00 元。

3. 對於實體提取，每頁總共有三條文本記錄。 每筆記錄是 1,000 個字元。 每頁三條文本記錄乘以 6，000 頁等於 18，000 條文本記錄。 假設每 1,000 筆文字記錄需要美金 $2.00 元，則此步驟需要美金 $36.00 元。

綜上所述，您需要支付約 57.00 美元，以便使用所述技能集攝錄 1，000 個此類 PDF 文檔。

## <a name="next-steps"></a>後續步驟
+ [Azure 認知搜索定價頁](https://azure.microsoft.com/pricing/details/search/)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [建立技能集 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何對應豐富型欄位](cognitive-search-output-field-mapping.md) (英文)
