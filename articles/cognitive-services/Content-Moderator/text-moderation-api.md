---
title: 文字仲裁 - Content Moderator
titleSuffix: Azure Cognitive Services
description: 對可能不需要的文本、個人數據和自定義術語清單使用文本審核。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 41e88dd5a08de485f770559959843ba3b54e590f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274005"
---
# <a name="learn-text-moderation-concepts"></a>了解文字仲裁概念

使用內容審閱者的計算機輔助文本審閱和[人工審閱](Review-Tool-User-Guide/human-in-the-loop.md)功能來調節文本內容。

您可以根據您的原則及閾值來封鎖、核准或檢閱內容。 使用它來增強合作夥伴、員工和取用者產生之文字內容所在環境的人工仲裁審核。 這些環境包括聊天室、討論區、聊天機器人、電子商務目錄和文件。 

服務回應會包含下列資訊：

- 粗話：搭配內建的多語言粗話字詞清單進行字詞型比對
- 分類：由電腦輔助分類來分成三種類別
- 個人資料
- 自動校正的文字
- 原始文字
- Language

## <a name="profanity"></a>粗話

如果 API 偵測到以任何[支援的語言](Text-Moderation-API-Languages.md)表達的任何粗話字詞，這些字詞就會包含在回應中。 此回應也會包含它們在原始文字中的位置 (`Index`)。 以下範例 JSON 中的 `ListId` 係指在 [自訂字詞清單](try-terms-list-api.md) \(如果有的話\) 中找到的字詞。

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> 針對 **language** 參數，請指派 `eng` 或將其保留空白，以查看電腦輔助**分類** 回應 (預覽版功能)。 **此功能僅支援英文**。
>
> 針對**粗話字詞**偵測，請使用本文中所列支援語言的 [ISO 639-3 代碼](http://www-01.sil.org/iso639-3/codes.asp)或將其保留空白。

## <a name="classification"></a>分類

內容審閱者的計算機輔助**文本分類功能****僅支援英語**,有助於檢測可能不需要的內容。 所標幟的內容可能是依據上下文而被評估為不當的內容。 它會傳遞每個類別的可能性，而且可能會建議人工審核。 此功能使用定型模型來識別可能的濫用、毀謗性或歧視性語言。 這包括要供審核的俚語、縮寫單字、冒犯性及刻意拼錯的單字。 

以下 JSON 擷取內容顯示一個範例輸出︰

    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
              "Score": 1.5113095059859916E-06
            },
        "Category2": {
              "Score": 0.12747249007225037
            },
        "Category3": {
              "Score": 0.98799997568130493
        }
    }

### <a name="explanation"></a>說明

- `Category1` 指的是可能有在特定情況下被視為明顯色情或成人內容的語言存在。
- `Category2` 指的是可能有在特定情況下被視為具性暗示或成人內容的語言存在。
- `Category3` 指的是可能有在特定情況下被視為具冒犯性的語言存在。
- `Score` 介於 0 到 1 之間。 分數越高，模型預測為適用該類別的可能性就越高。 此功能須倚賴統計模型，而不是手動編碼的結果。 建議您使用自己的內容進行測試，以判斷每個類別如何符合您的需求。
- `ReviewRecommended` 會是 true 或 false，視內部分數閾值而定。 客戶應該評估是要使用此值，還是根據其內容原則決定自訂閾值。

## <a name="personal-data"></a>個人資料

個人資料功能可偵測此資訊的潛在存在:

- 電子郵件地址
- 美國郵寄位址
- IP 位址
- 美國電話號碼

以下範例顯示一個範例回應：

```json
"pii":{
  "email":[
      {
        "detected":"abcdef@abcd.com",
        "sub_type":"Regular",
        "text":"abcdef@abcd.com",
        "index":32
      }
  ],
  "ssn":[

  ],
  "ipa":[
      {
        "sub_type":"IPV4",
        "text":"255.255.255.255",
        "index":72
      }
  ],
  "phone":[
      {
        "country_code":"US",
        "text":"6657789887",
        "index":56
      }
  ],
  "address":[
      {
        "text":"1 Microsoft Way, Redmond, WA 98052",
        "index":89
      }
  ]
}
```

## <a name="auto-correction"></a>自動校正

假設輸入文本為("lzay"和"f0x"是有意的):

    The qu!ck brown f0x jumps over the lzay dog.

如果您要求自動校正，回應就會包含該文字的校正版：

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>建立及管理您的自訂字詞清單

雖然預設的全域字詞清單適用於大多數案例，但您可能會想要依據業務需求特定的字詞來進行過濾。 例如，您可能會想要從使用者的文章中篩選掉任何競爭的品牌名稱。

> [!NOTE]
> 上限是 **5 個字詞清單**，其中每個清單**不可超過 10,000 個字詞**。
>

以下範例顯示相符的「清單識別碼」：

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator 有提供一個[字詞清單 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)，其中含有可管理自訂字詞清單的作業。 請從[字詞清單 API 主控台](try-terms-list-api.md)開始著手，然後使用 REST API 程式碼範例。 此外，如果您已熟悉 Visual Studio 和 C#，請一併參閱[字詞清單 .NET 快速入門](term-lists-quickstart-dotnet.md)。

## <a name="next-steps"></a>後續步驟

試用[文字仲裁 API 主控台](try-text-api.md)，並使用 REST API 程式碼範例。 如果您熟悉 Visual Studio 和 C#,請查看[.NET SDK 快速入門](dotnet-sdk-quickstart.md)的文本審核部分。
