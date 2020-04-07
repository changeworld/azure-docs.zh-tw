---
title: 工作階段管理 REST API
description: 描述如何管理工作階段
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681242"
---
# <a name="use-the-session-management-rest-api"></a>使用工作階段管理 REST API

要使用 Azure 遠端呈現功能,需要建立*工作階段*。 每個會話對應於在 Azure 中分配的虛擬機器 (VM) 並等待用戶端設備連接。 當設備連接時,VM 呈現請求的數據,並將結果作為視頻流提供。 在建立工作階段期間,您選擇要運行的伺服器類型,這決定了定價。 一旦不再需要會話,就應該停止會話。 如果不手動停止,則在會話的*租約時間*到期時,它將自動關閉。

我們在*腳本*資料夾中的[ARR 示例存儲庫](https://github.com/Azure/azure-remote-rendering)(稱為*呈現會話.ps1)* 中提供了 PowerShell 腳本,該腳本演示了我們服務的使用方式。 此處介紹了文本及其設定:[範例 PowerShell 文稿](../samples/powershell-example-scripts.md)

> [!TIP]
> 此頁上列出的 PowerShell 命令旨在相互補充。 如果在同一 PowerShell 命令提示符中按順序運行所有腳本,它們將相互構建。

## <a name="regions"></a>區域

請參考要將要求傳送到的基本網址[的區域清單](../reference/regions.md)。

對於下面的示例腳本,我們選擇了*西烏斯2*區域。

### <a name="example-script-choose-an-endpoint"></a>範例文稿:選擇終結點

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>帳戶

如果沒有遠端呈現帳號,[請建立一個](create-an-account.md)。 每個資源都由*一個帳戶 Id*標識,該帳戶 Id 在整個會話 API 中使用。

### <a name="example-script-set-accountid-and-accountkey"></a>範例文稿:設定帳戶 Id 和帳戶金鑰

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>常見要求標頭

* *授權*標頭的值必須`Bearer TOKEN`為 ",`TOKEN`其中" 是[安全令牌服務返回的](tokens.md)身份驗證權杖。

### <a name="example-script-request-a-token"></a>範例文稿:要求權杖

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>常見回應標頭

* 產品團隊可以使用*MS-CV*標頭追蹤服務中的調用。

## <a name="create-a-session"></a>建立工作階段

此命令創建工作階段。 它返回新會話的 ID。 所有其他命令都需要會話 ID。

| URI | 方法 |
|-----------|:-----------|
| /v1/帳戶/*帳戶 Id*/工作階段/創建 | POST |

**要求正文:**

* 最大租賃時間(時間跨度):自動停用 VM 時的超時值
* 模型(陣列):要預載入的資產容器網址
* 大小(字串):VM 大小 **("標準"** 或 **"進階")。** 請參考特定的[VM 大小限制](../reference/limits.md#overall-number-of-polygons)。

**反應:**

| 狀態碼 | JSON 承載 | 註解 |
|-----------|:-----------|:-----------|
| 202 | - 工作階段 Id: GUID | Success |

### <a name="example-script-create-a-session"></a>範例文稿:建立工作階段

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

### <a name="example-script-store-sessionid"></a>範例文稿:儲存工作階段 Id

來自上述請求的回應包括會話**Id**,您需要用於所有後續請求。

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>更新工作階段

此命令更新工作階段的參數。 目前,您只能延長會話的租約時間。

> [!IMPORTANT]
> 自會話開始以來,租賃時間始終作為總時間給出。 這意味著,如果您創建了一個租約時間為一小時的會話,並且希望將其租約時間再延長一小時,則必須將其最大租約時間更新為兩小時。

| URI | 方法 |
|-----------|:-----------|
| /v1/帳戶/*帳戶 ID*/工作階段/*工作階段 Id* | PATCH |

**要求正文:**

* 最大租賃時間(時間跨度):自動停用 VM 時的超時值

**反應:**

| 狀態碼 | JSON 承載 | 註解 |
|-----------|:-----------|:-----------|
| 200 | | Success |

### <a name="example-script-update-a-session"></a>範例文稿:更新工作階段

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

## <a name="get-active-sessions"></a>取得活動工作階段

此命令返回活動工作階段的清單。

| URI | 方法 |
|-----------|:-----------|
| /v1/帳戶/*帳戶 Id*/工作階段 | GET |

**反應:**

| 狀態碼 | JSON 承載 | 註解 |
|-----------|:-----------|:-----------|
| 200 | - 工作階段:工作階段屬性陣列 | 有關工作階段屬性的說明,請參閱「獲取會話屬性」部分 |

### <a name="example-script-query-active-sessions"></a>範例文稿:查詢活動工作階段

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

## <a name="get-sessions-properties"></a>取得工作階段屬性

此命令返回有關會話的資訊,例如其 VM 主機名。

| URI | 方法 |
|-----------|:-----------|
| /v1/帳戶/*帳戶 Id*/工作階段/*工作階段 Id*/屬性 | GET |

**反應:**

| 狀態碼 | JSON 承載 | 註解 |
|-----------|:-----------|:-----------|
| 200 | - 訊息:字串<br/>- 工作階段經過時間:時間跨度<br/>- 工作階段主機名稱:字串<br/>- 工作階段 Id:字串<br/>- 會話最大租賃時間:時間跨度<br/>- 工作階段大小:Enle<br/>- 工作階段狀態:枚舉 | 關閉的工作階段狀態 ( 已執行, 就緒、 停止、 停止、 過期、 錯誤]<br/>如果狀態為「錯誤」或「過期」,則郵件將包含更多資訊 |

### <a name="example-script-get-session-properties"></a>範例文稿:取得工作階段屬性

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

此命令停止作業階段。 分配的 VM 將在不久後回收。

| URI | 方法 |
|-----------|:-----------|
| /v1/帳戶/*帳戶 Id*/工作階段/*工作階段 Id* | 刪除 |

**反應:**

| 狀態碼 | JSON 承載 | 註解 |
|-----------|:-----------|:-----------|
| 204 | | Success |

### <a name="example-script-stop-a-session"></a>示例文本:停止會話

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

* [電源外殼文稿範例](../samples/powershell-example-scripts.md)
