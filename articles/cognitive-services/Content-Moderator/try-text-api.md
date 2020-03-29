---
title: 使用文字仲裁 API 來仲裁文字 - Content Moderator
titleSuffix: Azure Cognitive Services
description: 使用線上主控台中的「文字仲裁 API」來試用文字仲裁。
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e0930558f31b27a77fa2cd6b44fcea2fe9091086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74538815"
---
# <a name="moderate-text-from-the-api-console"></a>從 API 主控台仲裁文字

使用 Azure 內容審閱人中的[文本審核 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)掃描文本內容的褻瀆性，並將其與自訂清單和共用清單進行比較。

## <a name="get-your-api-key"></a>取得 API 金鑰

您需要有訂用帳戶金鑰，才能在線上主控台中試用 API。 這位於 [設定]**** 索引標籤的 [Ocp-Apim-Subscription-Key]**** 方塊中。 如需詳細資訊，請參閱[概觀](overview.md)。

## <a name="navigate-to-the-api-reference"></a>瀏覽至 API 參考

移至[文字仲裁 API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)。 

  [Text - Screen] \(文字 - 過濾\)**** 頁面隨即開啟。

## <a name="open-the-api-console"></a>開啟 API 主控台

針對 [Open API testing console] \(開啟 API 測試主控台\)**** 中，選取最能描述您位置的區域。 

  ![[Text - Screen] \(文字 - 過濾\) 頁面區域選取項目](images/test-drive-region.png)

  [Text - Screen] \(文字 - 過濾\)**** API 主控台隨即開啟。

## <a name="select-the-inputs"></a>選取輸入

### <a name="parameters"></a>參數

選取您想要在文字過濾中使用的查詢參數。 就此範例而言，針對 **language**，請使用預設值。 您也可以將其保留空白，因為此作業會在其執行過程中自動偵測可能的語言。

> [!NOTE]
> 針對 **language** 參數，請指派 `eng` 或將其保留空白，以查看電腦輔助**分類** 回應 (預覽版功能)。 **此功能僅支援英文**。
>
> 針對**粗話字詞**偵測，請使用本文中所列支援語言的 [ISO 639-3 代碼](http://www-01.sil.org/iso639-3/codes.asp)或將其保留空白。

針對 **autocorrect****PII**及 **classify (預覽版)**，請選取 **true**。 將 [ListId]**** 欄位保留空白。

  ![[Text - Screen] \(文字 - 過濾\) 主控台查詢參數](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>內容類型

針對 **Content-Type**，選取您想要過濾的內容類型。 就此範例而言，請使用預設的 **text/plain** 內容類型。 在 [Ocp-Apim-Subscription-Key]**** 中，輸入您的訂用帳戶金鑰。

### <a name="sample-text-to-scan"></a>要掃描的範例文字

在 [Request body] \(要求本文\)**** 方塊中，輸入一些文字。 以下範例示範文字中刻意安排的錯字。

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>分析回應

以下回應顯示來自 API 的各種深入解析。 它包含潛在的褻瀆、個人資料、分類（預覽）和自動校正版本。

> [!NOTE]
> 電腦輔助「分類」功能目前為預覽版且僅支援英文。

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.\r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123.\r\nAlso, 999-99-9999 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 4255550111, IP: 255. 255. 255. 255, 1234 Main Boulevard, Panapolis WA 96555. \r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123. \r\nAlso, 999- 99- 9999 looks like a social security number ( SSN) .",
"Misrepresentation":null,
"PII":{  
  "Email":[  
    {  
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[  
    {  
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[  
    {  
      "CountryCode":"US",
      "Text":"4255550111",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"425 555 0111",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 123 456 7890",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1234 Main Boulevard, Panapolis WA 96555",
      "Index":89
    }
  ],
  "SSN":[  
    {  
      "Text":"999999999",
      "Index":56
    },
    {  
      "Text":"999-99-9999",
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

有關 JSON 回應中所有部分的詳細說明，請參閱[文本審核](text-moderation-api.md)概念指南。

## <a name="next-steps"></a>後續步驟

在代碼中使用 REST API，或按照[.NET SDK 快速入門](dotnet-sdk-quickstart.md)與應用程式集成。
