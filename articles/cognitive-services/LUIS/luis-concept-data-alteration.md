---
title: 資料改變-LUIS
description: 了解如何在於 Language Understanding (LUIS) 中進行預測之前變更資料
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: b3b36351a64a4e1a0bd13d5785a4e0609a80901d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80292070"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>預測之前或預測期間變更語句資料
LUIS 提供可在預測之前或預測期間操作語句的方法。 其中包括[修正拼寫](luis-tutorial-bing-spellcheck.md)，以及修正預先建立之[datetimeV2](luis-reference-prebuilt-datetimev2.md)的時區問題。

## <a name="correct-spelling-errors-in-utterance"></a>校正語句中的拼字錯誤


### <a name="v3-runtime"></a>V3 執行時間

在您將語句傳送至 LUIS 之前，預先處理文字以進行拼寫更正。 使用範例語句搭配正確的拼寫，以確保您得到正確的預測。

使用[Bing 拼寫檢查](../bing-spell-check/overview.md)來更正文字，然後再將它傳送至 LUIS。

### <a name="prior-to-v3-runtime"></a>在 V3 執行時間之前

LUIS 使用 [Bing 拼字檢查 API V7](../Bing-Spell-Check/overview.md) 來校正語句中的拼字錯誤。 LUIS 需要與該服務相關的金鑰。 請建立金鑰，然後在[端點](https://go.microsoft.com/fwlink/?linkid=2092356)新增該金鑰作為查詢字串參數。

端點必須有兩個參數，才能讓拼字校正運作：

|Param|值|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[Bing 拼字檢查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 端點金鑰|

當 [Bing 拼字檢查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 偵測到錯誤時，系統會將原始語句和校正後語句及預測一起從端點傳回。

#### <a name="v2-prediction-endpoint-response"></a>[V2 預測端點回應](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 預測端點回應](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>允許的單字清單
LUIS 中使用的 Bing 拼寫檢查 API 不支援在拼寫檢查改變期間忽略的字詞清單。 如果您需要允許單字或縮略字清單，請先處理用戶端應用程式中的語句，再將語句傳送至 LUIS 進行意圖預測。

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>變更預先建置 datetimeV2 實體的時區
當 LUIS 應用程式使用預先建立的[datetimeV2](luis-reference-prebuilt-datetimev2.md)實體時，可以在預測回應中傳回 datetime 值。 要求的時區會用來判斷要傳回的正確日期時間。 如果要求來自 Bot 或另一個集中式應用程式，請在其抵達 LUIS 之前，先更正 LUIS 使用的時區。

### <a name="endpoint-querystring-parameter"></a>端點查詢字串參數
更正時區的方式是使用 `timezoneOffset` 參數將使用者時區新增至[端點](https://go.microsoft.com/fwlink/?linkid=2092356)。 `timezoneOffset` 的值應該是用以變更時間的正數或負數 (以分鐘為單位)。

|Param|值|
|--|--|
|`timezoneOffset`|正數或負數 (以分鐘為單位)|

### <a name="daylight-savings-example"></a>日光節約範例
如果您需要讓傳回的預先建置 datetimeV2 針對日光節約時間進行調整，則應該針對[端點](https://go.microsoft.com/fwlink/?linkid=2092356)查詢，使用 `timezoneOffset` 查詢字串參數搭配以分鐘為單位的 +/- 值。

#### <a name="v2-prediction-endpoint-request"></a>[V2 預測端點要求](#tab/V2)

增加 60 分鐘：

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

減去 60 分鐘：

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v3-prediction-endpoint-request"></a>[V3 預測端點要求](#tab/V3)

增加 60 分鐘：

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

減去 60 分鐘：

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

深入了解 [V3 預測端點](luis-migration-api-v3.md)。

* * *

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C# 程式碼可判斷 timezoneOffset 的正確值
下列 C# 程式碼使用 [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) 類別的 [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) 方法，根據系統時間判斷正確的 `timezoneOffset`：

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用此教學課程來校正拼字錯誤](luis-tutorial-bing-spellcheck.md)
