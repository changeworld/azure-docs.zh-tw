---
title: 會話管理 REST API
description: 說明如何管理會話
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 0af9d6906e038a4b9285a2c302fc0c98345fdbd9
ms.sourcegitcommit: 70ee014d1706e903b7d1e346ba866f5e08b22761
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90023749"
---
# <a name="use-the-session-management-rest-api"></a>使用工作階段管理 REST API

若要使用 Azure 遠端轉譯的功能，您必須建立一個 *會話*。 每個會話都對應至虛擬機器， (VM) 在 Azure 中配置並等候用戶端裝置連線。 當裝置連線時，VM 會轉譯要求的資料，並以影片串流的形式提供結果。 在會話建立期間，您會選擇要執行的伺服器類型，以決定定價。 一旦不再需要會話，應停止。 如果未手動停止，則會在會話的 *租用時間* 到期時自動關閉。

我們在*腳本*資料夾中的[ARR 範例存放庫](https://github.com/Azure/azure-remote-rendering)中提供 PowerShell 腳本，稱為*RenderingSession.ps1*，其示範如何使用我們的服務。 腳本及其設定如下所述： [範例 PowerShell 腳本](../samples/powershell-example-scripts.md)

> [!TIP]
> 此頁面上所列的 PowerShell 命令是為了彼此互補。 如果您在相同的 PowerShell 命令提示字元中依序執行所有腳本，它們會在彼此之上建立。

## <a name="regions"></a>區域

查看要將要求傳送至的基底 Url 的 [可用區域清單](../reference/regions.md) 。

針對下面的範例腳本，我們選擇了區域 *westus2*。

### <a name="example-script-choose-an-endpoint"></a>範例腳本：選擇端點

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>帳戶

如果您沒有遠端轉譯帳戶，請 [建立一個](create-an-account.md)。 每個資源都是由 *accountId*（在整個會話 api 中使用）來識別。

### <a name="example-script-set-accountid-and-accountkey"></a>範例腳本：設定 accountId 和 accountKey

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>常見的要求標頭

* *授權*標頭的值必須為 " `Bearer TOKEN` "，其中 " `TOKEN` " 是[安全權杖服務所傳回](tokens.md)的驗證權杖。

### <a name="example-script-request-a-token"></a>範例腳本：要求權杖

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>常見的回應標頭

* 「 *MS CV* 」標頭可供產品小組用來追蹤服務內的呼叫。

## <a name="create-a-session"></a>建立工作階段

此命令會建立會話。 它會傳回新會話的識別碼。 您需要所有其他命令的會話識別碼。

| URI | 方法 |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/create | POST |

**要求主體：**

* maxLeaseTime (timespan) ：會話將自動解除委任時的超時值
* 模型 (陣列) ：要預先載入的資產容器 Url
* 大小 (字串) ：要設定 ([**"standard"**](../reference/vm-sizes.md) 或 [**"premium"**](../reference/vm-sizes.md)) 的伺服器大小。 請參閱特定 [大小的限制](../reference/limits.md#overall-number-of-polygons)。

**反應：**

| 狀態碼 | JSON 承載 | 註解 |
|-----------|:-----------|:-----------|
| 202 | -sessionId： GUID | Success |

### <a name="example-script-create-a-session"></a>範例腳本：建立會話

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

範例輸出︰

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>範例腳本：存放區 sessionId

上述要求的回應包含 **sessionId**，您需要所有後續的要求。

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="modify-and-query-session-properties"></a>修改和查詢會話屬性

有幾個命令可查詢或修改現有會話的參數。

> [!CAUTION]
> 針對所有 REST 呼叫，太頻繁傳送這些命令會導致伺服器節流並最後傳回失敗。 此案例中的狀態碼是 429 ( 「太多要求」 ) 。 根據經驗法則，**後續的呼叫之間應該會有 5-10 秒**的延遲。

### <a name="update-session-parameters"></a>更新會話參數

此命令會更新會話的參數。 目前您只能延長會話的租用時間。

> [!IMPORTANT]
> 租用時間一律會指定為從會話開始以來的總時間。 這表示，如果您建立的會話的租用時間為一小時，而您想要將其租用時間延長為另一個小時，您必須將其 maxLeaseTime 更新為兩小時。

| URI | 方法 |
|-----------|:-----------|
| /v1/accounts/*accountID*/sessions/*sessionId* | PATCH |

**要求主體：**

* maxLeaseTime (timespan) ：會話將自動解除委任時的超時值

**反應：**

| 狀態碼 | JSON 承載 | 註解 |
|-----------|:-----------|:-----------|
| 200 | | Success |

#### <a name="example-script-update-a-session"></a>範例腳本：更新會話

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

範例輸出︰

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

### <a name="get-active-sessions"></a>取得使用中會話

此命令會傳回作用中會話的清單。

| URI | 方法 |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions | GET |

**反應：**

| 狀態碼 | JSON 承載 | 註解 |
|-----------|:-----------|:-----------|
| 200 | -會話：會話屬性的陣列 | 如需會話屬性的說明，請參閱「取得會話屬性」一節。 |

#### <a name="example-script-query-active-sessions"></a>範例腳本：查詢使用中會話

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

範例輸出︰

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

### <a name="get-sessions-properties"></a>取得會話屬性

此命令會傳回會話的相關資訊，例如其 VM 主機名稱。

| URI | 方法 |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/*sessionId*/properties | GET |

**反應：**

| 狀態碼 | JSON 承載 | 註解 |
|-----------|:-----------|:-----------|
| 200 | -message： string<br/>-sessionElapsedTime： timespan<br/>-sessionHostname：字串<br/>-sessionId： string<br/>-sessionMaxLeaseTime： timespan<br/>-sessionSize： enum<br/>-sessionStatus： enum | 列舉 sessionStatus {正在啟動、就緒、停止、已停止、已過期、錯誤}<br/>如果狀態為 [錯誤] 或 [已過期]，訊息將會包含詳細資訊 |

#### <a name="example-script-get-session-properties"></a>範例腳本：取得會話屬性

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

範例輸出︰

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>停止工作階段

此命令會停止會話。 配置的 VM 會在不久後立即回收。

| URI | 方法 |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/*sessionId* | 刪除 |

**反應：**

| 狀態碼 | JSON 承載 | 註解 |
|-----------|:-----------|:-----------|
| 204 | | Success |

### <a name="example-script-stop-a-session"></a>範例腳本：停止會話

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

範例輸出︰

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>後續步驟

* [PowerShell 指令碼範例](../samples/powershell-example-scripts.md)
