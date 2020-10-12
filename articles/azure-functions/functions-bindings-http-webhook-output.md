---
title: Azure Functions HTTP 輸出系結
description: 瞭解如何在 Azure Functions 中傳回 HTTP 回應。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84697437"
---
# <a name="azure-functions-http-output-bindings"></a>Azure Functions HTTP 輸出系結

使用 HTTP 輸出繫結來回應 HTTP 要求傳送者。 此繫結需要 HTTP 觸發程序，並可讓您自訂與觸發程序要求相關聯的回應。

HTTP 觸發函式的預設傳回值為：

- `HTTP 204 No Content` 具有 Functions 2.x 和更新版本中的空白主體
- `HTTP 200 OK` 具有 Functions 1.x 中的空白主體

## <a name="configuration"></a>組態

下表說明您在 function.json 檔案中設定的繫結設定屬性。 就 C# 類別程式庫而言，沒有任何屬性 (Attribute) 的屬性 (Property) 與這些 *function.json* 屬性 (Property) 對應。

|屬性  |描述  |
|---------|---------|
| **type** |必須設為 `http`。 |
| **direction** | 必須設為 `out`。 |
| **name** | 函式程式碼中用於回應的變數名稱，或要使用傳回值的 `$return`。 |

## <a name="usage"></a>使用量

若要傳送 HTTP 回應，請使用語言標準回應模式。 在 C# 或 C# 指令碼 中，讓函式傳回類型成為 `IActionResult` 或 `Task<IActionResult>`。 在 C# 中，傳回值屬性並非必要。

如需範例回應，請參閱[觸發程序範例](./functions-bindings-http-webhook-trigger.md#example)。

## <a name="hostjson-settings"></a>host.json 設定

本節說明2.x 版和更高版本中此系結的可用通用設定。 下列檔案的範例 host.js只包含此系結的2.x 版 + 設定。 如需2.x 版和更高版本中全域設定設定的詳細資訊，請參閱 [ Azure Functions 的參考host.js](functions-host-json.md)。

> [!NOTE]
> 有關 Functions 1.x 中 host.json 的參考，請參閱[適用於 Azure Functions 1.x 的 host.json 參考](functions-host-json-v1.md#http)。

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
| customHeaders|無|可讓您設定 HTTP 回應中的自訂標頭。 先前的範例會將 `X-Content-Type-Options` 標頭新增至回應，以避免內容類型探查。 |
|dynamicThrottlesEnabled|真<sup>\*</sup>|啟用時，此設定會使要求處理管線定期檢查系統效能計數器， `connections/threads/processes/memory/cpu/etc` 如果其中有任何計數器超過內建的高臨界值 (80% ) ，則要求將會被拒絕， `429 "Too Busy"` 直到計數器 (s) 回到正常層級為止。<br/><sup>\*</sup>取用方案中的預設值為 `true` 。 專用方案中的預設值為 `false` 。|
|hsts|未啟用|當 `isEnabled` 設定為時 `true` ，會強制執行[HTTP Strict TRANSPORT Security (HSTS) .net Core 的行為](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts)，如[ `HstsOptions` 類別](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0)中所定義。 上述範例也會將 [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) 屬性設定為10天。 支援的屬性 `hsts` 為： <table><tr><th>屬性</th><th>說明</th></tr><tr><td>excludedHosts</td><td>未新增 HSTS 標頭之主機名稱的字串陣列。</td></tr><tr><td>includeSubDomains</td><td>布林值，指出是否已啟用 Strict-Transport-Security 標頭的 includeSubDomain 參數。</td></tr><tr><td>maxAge</td><td>定義 Strict-Transport-Security 標頭之最大壽命參數的字串。</td></tr><tr><td>預先載入</td><td>指出是否已啟用 Strict-Transport-Security 標頭之預先載入參數的布林值。</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|平行執行的 HTTP 函式數目上限。 此值可讓您控制平行存取，有助於管理資源使用率。 例如，您的 HTTP 函式可能會使用大量的系統資源 (記憶體/cpu/通訊端，) 使得平行存取過高時發生問題。 或者，您可能有一個函式對協力廠商服務提出輸出要求，而這些呼叫必須有速率限制。 在這些情況下，套用節流會有所幫助。 <br/><sup>*</sup>使用量方案的預設值是100。 專用方案的預設值是未系結的 (`-1`) 。|
|maxOutstandingRequests|200<sup>\*</sup>|在任何指定時間保留的未完成要求數目上限。 此限制包括已排入佇列但尚未開始執行的要求，以及任何進行中的執行。 會以 429「忙碌」回應來拒絕任何超過此限制的連入要求。 這樣可讓呼叫者採用以時間為基礎的重試策略，並且也協助您控制要求延遲的上限。 此動作只會控制在指令碼主機執行路徑內發生的佇列處理。 其他佇列 (例如 ASP.NET 要求佇列) 仍然有效，且不受此設定的影響。 <br/><sup>\*</sup>使用量方案的預設值是200。 專用方案的預設值是未系結的 (`-1`) 。|
|routePrefix|api|適用於所有路由的路由前置詞。 若要移除預設前置詞，請使用空字串。 |

## <a name="next-steps"></a>接下來的步驟

- [從 HTTP 要求執行函數](./functions-bindings-http-webhook-trigger.md)