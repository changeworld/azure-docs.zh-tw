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
ms.openlocfilehash: ad365c2d4c171105d8dec89d818ef481361d1ff8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81272588"
---
# <a name="moderate-text-from-the-api-console"></a>從 API 主控台仲裁文字

使用 Azure 內容仲裁中的[文字仲裁 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)來掃描您的文字內容是否不雅，並與自訂和共用清單進行比較。

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
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>分析回應

以下回應顯示來自 API 的各種深入解析。 它包含潛在的不雅內容、個人資料、分類（預覽），以及自動校正的版本。

> [!NOTE]
> 電腦輔助「分類」功能目前為預覽版且僅支援英文。

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
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
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

如需 JSON 回應中所有區段的詳細說明，請參閱[文字審核](text-moderation-api.md)概念指南。

## <a name="next-steps"></a>後續步驟

在您的程式碼中使用 REST API，或遵循[.NET SDK 快速入門](dotnet-sdk-quickstart.md)，與您的應用程式進行整合。
