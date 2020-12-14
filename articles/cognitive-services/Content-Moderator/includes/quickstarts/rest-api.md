---
title: Content Moderator REST API 快速入門
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，了解如何開始使用 Azure Content Moderator REST API。 在應用程式中建置內容篩選軟體，以遵循法規或維護使用者應有的環境。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/08/2020
ms.author: pafarley
ms.openlocfilehash: 06af722e8c827acdad356acb982ac3761ef68236
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905653"
---
開始使用 Azure Content Moderator REST API。 

Content Moderator 是一種 AI 服務，可讓您處理可能具冒犯意味、有風險或不當的資料。 使用 AI 驅動的內容審核服務來掃描文字、影像和影片，並自動套用內容旗標。 然後，將您的應用程式與檢閱工具整合，這是人工檢閱者小組的線上仲裁環境。 在應用程式中建置內容篩選軟體，以遵循法規或維護使用者應有的環境。

使用 Content Moderator REST API 執行下列動作：

* [仲裁文字](#moderate-text)
* [仲裁影像](#moderate-images)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="建立 Content Moderator 資源"  target="_blank">建立 Content Moderator 資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 Content Moderator。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。


## <a name="moderate-text"></a>仲裁文字

您將使用如下所示的命令呼叫 Content Moderator API 來分析文字主體，並將結果列印到主控台。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="textmod":::

將命令複製到文字編輯器，並進行下列變更：

1. 將 `Ocp-Apim-Subscription-Key` 指派至您的有效臉部訂用帳戶金鑰。
1. 變更查詢 URL 的第一個部分，以符合與您訂用帳戶金鑰對應的端點。
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. 選擇性地將要求主體變更為您想要分析的任何文字字串。

完成變更之後，請開啟命令提示字元並輸入新的命令。 

### <a name="examine-the-results"></a>檢查結果

您應該會在主控台視窗中看到顯示為 JSON 資料的文字仲裁結果。 例如：

```json
{
  "OriginalText": "Is this a crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255,\n1 Microsoft Way, Redmond, WA 98052\n",
  "NormalizedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "AutoCorrectedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "Misrepresentation": null,
  "PII": {
    "Email": [
      {
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 21
      }
    ],
    "IPA": [
      {
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 61
      }
    ],
    "Phone": [
      {
        "CountryCode": "US",
        "Text": "6657789887",
        "Index": 45
      }
    ],
    "Address": [
      {
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 78
      }
    ]
  },
 "Classification": {
    "Category1": 
    {
      "Score": 0.5
    },
    "Category2": 
    {
      "Score": 0.6
    },
    "Category3": 
    {
      "Score": 0.5
    },
    "ReviewRecommended": true
  },
  "Language": "eng",
  "Terms": [
    {
      "Index": 10,
      "OriginalIndex": 10,
      "ListId": 0,
      "Term": "crap"
    }
  ],
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "1717c837-cfb5-4fc0-9adc-24859bfd7fac"
}
```

若要進一步了解 Content Moderator 所過濾的文字屬性，請參閱[文字仲裁概念](../../text-moderation-api.md)指南。

## <a name="moderate-images"></a>仲裁影像

您將使用如下所示的命令呼叫 Content Moderator API 來仲裁遠端影像，並將結果列印到主控台。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="imagemod":::

將命令複製到文字編輯器，並進行下列變更：

1. 將 `Ocp-Apim-Subscription-Key` 指派至您的有效臉部訂用帳戶金鑰。
1. 變更查詢 URL 的第一個部分，以符合與您訂用帳戶金鑰對應的端點。
1. 選擇性地將要求主體中的 `"Value"` URL 變更為您想要仲裁的任何遠端影像。

> [!TIP]
> 您也可以藉由將其位元組資料傳遞至要求主體，來仲裁本機影像。 若要了解如何執行此操作，請參閱[參考文件](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)。

完成變更之後，請開啟命令提示字元並輸入新的命令。 

### <a name="examine-the-results"></a>檢查結果

您應該會在主控台視窗中看到顯示為 JSON 資料的影像仲裁結果。 

```json
{
  "AdultClassificationScore": x.xxx,
  "IsImageAdultClassified": <Bool>,
  "RacyClassificationScore": x.xxx,
  "IsImageRacyClassified": <Bool>,
  "AdvancedInfo": [],
  "Result": false,
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "<Request Tracking Id>"
}
```

若要進一步了解 Content Moderator 所過濾的影像屬性，請參閱[影像仲裁概念](../../image-moderation-api.md)指南。

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 Content Moderator REST API 來執行仲裁工作。 接下來請閱讀概念性指南，以深入了解影像或其他媒體的仲裁。

* [影像仲裁概念](../../image-moderation-api.md)
* [文字仲裁概念](../../text-moderation-api.md)
* [什麼是 Azure Content Moderator？](../../overview.md)