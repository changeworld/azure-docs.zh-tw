---
title: Azure 函數 HTTP 輸出綁定
description: 瞭解如何在 Azure 函數中返回 HTTP 回應。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277618"
---
# <a name="azure-functions-http-output-bindings"></a>Azure 函數 HTTP 輸出綁定

使用 HTTP 輸出繫結來回應 HTTP 要求傳送者。 此繫結需要 HTTP 觸發程序，並可讓您自訂與觸發程序要求相關聯的回應。

HTTP 觸發函數的預設傳回值為：

- `HTTP 204 No Content`函數 2.x 及更高版本中的空實體
- `HTTP 200 OK`函數 1.x 中空實體

## <a name="configuration"></a>組態

下表說明您在 function.json** 檔案中設定的繫結設定屬性。 就 C# 類別程式庫而言，沒有任何屬性 (Attribute) 的屬性 (Property) 與這些 *function.json* 屬性 (Property) 對應。

|屬性  |描述  |
|---------|---------|
| **型別** |必須設為 `http`。 |
| **direction** | 必須設為 `out`。 |
| **名稱** | 函式程式碼中用於回應的變數名稱，或要使用傳回值的 `$return`。 |

## <a name="usage"></a>使用量

若要傳送 HTTP 回應，請使用語言標準回應模式。 在 C# 或 C# 指令碼 中，讓函式傳回類型成為 `IActionResult` 或 `Task<IActionResult>`。 在 C# 中，傳回值屬性並非必要。

如需範例回應，請參閱[觸發程序範例](./functions-bindings-http-webhook-trigger.md#example)。

## <a name="hostjson-settings"></a>host.json 設定

本節介紹 2.x 和更高版本中可用於此綁定的全域配置設置。 下面的示例 host.json 檔僅包含此綁定的版本 2.x+ 設置。 有關版本 2.x 及以後的全域配置設置的詳細資訊，請參閱[Azure 函數的 host.json 引用](functions-host-json.md)。

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
| 自訂標題|無|允許您在 HTTP 回應中設置自訂標頭。 前面的示例將`X-Content-Type-Options`標頭添加到回應中，以避免內容類型嗅探。 |
|dynamicThrottlesEnabled|真<sup>\*</sup>|啟用此選項後，此設置會導致請求處理管道定期檢查系統效能計數器，例如`connections/threads/processes/memory/cpu/etc`，如果這些計數器中的任何一個超過內置高閾值 （80%），則請求將被拒絕，然後`429 "Too Busy"`回應，直到計數器返回到正常水準。<br/><sup>\*</sup>消耗計畫中的預設值為`true`。 專用計畫中的預設值為`false`。|
|噓|未啟用|設置為`isEnabled`時`true`，將強制執行[.NET Core 的 HTTP 嚴格傳輸安全 （HSTS） 行為](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts)，如[`HstsOptions`類](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0)中定義的那樣。 上面的示例還將[`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge)屬性設置為 10 天。 支援的屬性`hsts`是： <table><tr><th>屬性</th><th>描述</th></tr><tr><td>排除主機</td><td>未為其添加 HSTS 標頭的主機名稱字串陣列。</td></tr><tr><td>包括子域</td><td>布林值，指示是否啟用了"嚴格傳輸安全"標頭的包含 SubDomain 參數。</td></tr><tr><td>最大年齡</td><td>定義嚴格傳輸安全標頭的最大年齡參數的字串。</td></tr><tr><td>預先載入</td><td>布林，指示是否啟用了"嚴格傳輸安全"標頭的預載入參數。</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|並存執行的最大 HTTP 函數數。 此值允許您控制併發性，這有助於管理資源利用率。 例如，您可能具有一個使用大量系統資源（記憶體/cpu/通訊端）的 HTTP 函數，以便當併發性過高時，它會導致問題。 或者，您可能具有向協力廠商服務發出出站請求的功能，並且這些調用需要限制速率。 在這些情況下，套用節流會有所幫助。 <br/><sup>*</sup>消耗計畫的預設值為 100。 專用計畫的預設值為無界 。`-1`|
|maxOutstandingRequests|200<sup>\*</sup>|在任何指定時間保留的未完成要求數目上限。 此限制包括已排入佇列但尚未開始執行的要求，以及任何進行中的執行。 會以 429「忙碌」回應來拒絕任何超過此限制的連入要求。 這樣可讓呼叫者採用以時間為基礎的重試策略，並且也協助您控制要求延遲的上限。 此動作只會控制在指令碼主機執行路徑內發生的佇列處理。 其他佇列 (例如 ASP.NET 要求佇列) 仍然有效，且不受此設定的影響。 <br/><sup>\*</sup>消耗計畫的預設值為 200。 專用計畫的預設值為無界 。`-1`|
|routePrefix|api|適用於所有路由的路由前置詞。 若要移除預設前置詞，請使用空字串。 |

## <a name="next-steps"></a>後續步驟

- [從 HTTP 要求運行函數](./functions-bindings-http-webhook-trigger.md)