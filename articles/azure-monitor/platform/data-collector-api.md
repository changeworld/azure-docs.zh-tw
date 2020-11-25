---
title: Azure 監視器 HTTP 資料收集器 API | Microsoft Docs
description: 您可以使用 Azure 監視器 HTTP 資料收集器 API，將 POST JSON 資料從任何可呼叫 REST API 的用戶端新增至 Log Analytics 工作區。 本文說明如何使用此 API，並提供如何使用不同的程式設計語言來發佈資料的範例。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/14/2020
ms.openlocfilehash: ab0ed536bd23aaf15d85af85e4f924bc2f51f3d4
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "96006622"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>使用 HTTP 資料收集器 API 將記錄資料傳送給 Azure 監視器 (公開預覽)
本文示範如何使用 HTTP 資料收集器 API 將記錄資料從 REST API 用戶端傳送給 Azure 監視器。  內容說明如何將您指令碼或應用程式所收集的資料格式化、將其包含在要求中，以及讓 Azure 監視器授權該要求。  提供的範例適用於 PowerShell、C# 及 Python。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Azure 監視器 HTTP 資料收集器 API 為公開預覽狀態。

## <a name="concepts"></a>概念
您可以在 Azure 監視器中使用 HTTP 資料收集器 API，將記錄資料從任何可呼叫 REST API 的用戶端傳送至 Log Analytics 工作區。  這可能是「Azure 自動化」中從 Azure 或另一個雲端收集管理資料的 Runbook ，也可能是一個使用 Azure 監視器來合併和分析記錄資料的替代管理系統。

Log Analytics 工作區中的所有資料都會以具有特定記錄類型的記錄形式儲存。  您需把要傳送給「HTTP 資料收集器 API」的資料格式化成多個採用 JSON 格式的記錄。  當您提交資料時，系統會在儲存機制中針對要求承載中的每個記錄建立個別的記錄。


![HTTP 資料收集器概觀](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>建立要求
若要使用「HTTP 資料收集器 API」，您需建立一個 POST 要求，此要求包含所要傳送且採用「JavaScript 物件標記法」(JSON) 格式的資料。  接下來的三個表格列出每個要求所需的屬性。 本文稍後會更詳細說明每個屬性。

### <a name="request-uri"></a>要求 URI
| 屬性 | 屬性 |
|:--- |:--- |
| 方法 |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| 內容類型 |application/json |

### <a name="request-uri-parameters"></a>要求 URI 參數
| 參數 | 描述 |
|:--- |:--- |
| CustomerID |Log Analytics 工作區的唯一識別碼。 |
| 資源 |API 資源名稱︰/api/logs。 |
| API 版本 |要使用於這個要求的 API 版本。 目前是 2016-04-01。 |

### <a name="request-headers"></a>要求標頭
| 標頭 | 描述 |
|:--- |:--- |
| 授權 |授權簽章。 本文稍後會說明如何建立 HMAC-SHA256 標頭。 |
| Log-Type |指定正在提交的資料記錄類型。 只能包含字母、數位和底線 (_) ，且不得超過100個字元。 |
| x-ms-date |處理要求的日期 (採用 RFC 1123 格式)。 |
| x-ms-AzureResourceId | 與資料相關聯之 Azure 資源的資源識別碼。 這會填入 [_ResourceId](./log-standard-columns.md#_resourceid) 屬性，並允許資料包含在 [資源內容](design-logs-deployment.md#access-mode) 查詢中。 如果未指定此欄位，資料就不會包含在資源內容查詢中。 |
| time-generated-field | 資料中包含資料項目時間戳記的欄位名稱。 如果您指定欄位，則其內容會用於 **TimeGenerated**。 如果未指定此欄位，則 **TimeGenerated** 的預設值是所擷取訊息的時間。 訊息欄位的內容應遵循 ISO 8601 格式 YYYY-MM-DDThh:mm:ssZ。 |

## <a name="authorization"></a>授權
任何對於 Azure 監視器 HTTP 資料收集器 API 的要求都必須包含授權標頭。 若要驗證要求，您必須使用提出要求之工作區的主要或次要金鑰來簽署要求。 然後，將該簽章當作要求的一部分傳遞。   

授權標頭的格式如下︰

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

WorkspaceID 是 Log Analytics 工作區的唯一識別碼。 Signature 是[雜湊式訊息驗證碼 (HMAC)](/dotnet/api/system.security.cryptography.hmacsha256?view=netcore-3.1)，該驗證碼從要求建構而來，而後使用 [SHA256 演算法](/dotnet/api/system.security.cryptography.sha256?view=netcore-3.1)進行計算。 接下來，您可使用 Base64 編碼方式進行編碼。

使用此格式來進行 **SharedKey** 簽章字串的編碼︰

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

以下是簽章字串的範例︰

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

如果您有此簽章字串，請使用 HMAC-SHA256 演算法在 UTF-8 編碼的字串上進行編碼，然後將結果編碼為 Base64。 使用此格式︰

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

後續各節中的範例有範例程式碼可協助您建立授權標頭。

## <a name="request-body"></a>Request body
訊息的主體必須採用 JSON。 它必須包含一或多個具有下列格式之屬性名稱和值組的記錄。 屬性名稱只能包含字母、數位和底線 (_) 。

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

您可以使用下列格式將多筆記錄分批放入單一要求中。 所有記錄都必須是相同的記錄類型。

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>記錄類型和屬性
當您透過 Azure 監視器 HTTP 資料收集器 API 提交資料時，您可以定義自訂記錄類型。 您目前無法將資料寫入至其他資料類型和解決方案所建立的現有記錄類型。 Azure 監視器會讀取內送資料，然後建立符合所輸入值資料類型的屬性。

對於資料收集器 API 的每個要求都必須包含具有記錄類型名稱的 **Log-Type** 標頭。 後置詞 **_CL** 會自動附加至您輸入的名稱，以便以自訂記錄檔形式與其他記錄檔類型區別。 例如，如果您輸入名稱 **MyNewRecordType**，則 Azure 監視器會建立一筆類型為 **MyNewRecordType_CL** 的記錄。 這有助於確保使用者建立的類型名稱與目前或未來 Microsoft 解決方案隨附的類型名稱之間沒有衝突。

為了識別屬性的資料類型，Azure 監視器會對屬性名稱新增後置詞。 如果屬性包含 null 值，此屬性則不會包含於該記錄。 下表列出屬性資料類型和對應的後置詞︰

| 屬性資料類型 | 後置詞 |
|:--- |:--- |
| String |_s |
| Boolean |_b |
| Double |_d |
| 日期/時間 |_t |
| GUID (儲存為字串)  |_g |

> [!NOTE]
> 看似 Guid 的字串值將會獲得 _g 後置詞，並格式化為 GUID，即使連入值未包含連字號也一樣。 例如，"8145d822-13a7-44ad-859c-36f31a84f6dd" 和 "8145d82213a744ad859c36f31a84f6dd" 都會儲存為 "8145d822-13a7-44ad-859c-36f31a84f6dd"。 這和另一個字串之間的唯一差異在於名稱中的 _g，以及插入的連字號（如果輸入中未提供）。 

Azure 監視器用於每個屬性的資料類型取決於新記錄的記錄類型是否已經存在。

* 如果記錄類型不存在，Azure 監視器會使用 JSON 類型推斷來判斷新記錄的各屬性資料類型，以建立新的記錄。
* 如果記錄類型已存在，Azure 監視器會嘗試根據現有屬性建立新記錄。 如果新記錄中屬性的資料類型不相符且無法轉換為現有類型，或者如果此記錄包含不存在的屬性，則 Azure 監視器會建立具有相關後置詞的新屬性。

例如，此提交項目會建立具有三個屬性 (**number_d**、**boolean_b** 和 **string_s**) 的記錄︰

![範例記錄 1](media/data-collector-api/record-01.png)

如果您接著提交下一個項目 (所有的值均格式化為字串)，則不會變更屬性。 這些值可以轉換為現有的資料類型︰

![範例記錄 2](media/data-collector-api/record-02.png)

但是，如果您再進行下一次提交，則 Azure 監視器會建立新的屬性 **boolean_d** 和 **string_d**。 無法轉換這些值︰

![範例記錄 3](media/data-collector-api/record-03.png)

如果您再提交以下項目，則在記錄類型建立前，Azure 監視器會建立具有 **number_s**、**boolean_s** 和 **string_s** 三個屬性的記錄。 在此項目中，每個初始值都格式化為字串︰

![範例記錄 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>保留的屬性
以下是保留的屬性，不應該用於自訂記錄類型。 如果您的裝載包含任何這些屬性名稱，您將會收到錯誤。

- tenant

## <a name="data-limits"></a>資料限制
在張貼至 Azure 監視器資料收集 API 的資料上有一些限制。

* 可張貼至 Azure 監視器資料收集器 API 的每個張貼項目大小上限為 30 MB。 這是單一張貼項目的大小限制。 如果資料是來自超出 30 MB 的單一張貼項目，您應該將資料分割成較小區塊，然後同時傳送它們。
* 欄位值的大小上限為 32 KB。 如果欄位值大於 32 KB，資料將會被截斷。
* 指定類型欄位的建議數目上限為 50。 對於使用性和搜尋體驗觀點而言，這是一個實際的限制。  
* Log Analytics 工作區中的資料表僅支援最多 500 個資料行 (在本文中稱為欄位)。 
* 資料行名稱的字元數目上限為 500。

## <a name="return-codes"></a>傳回碼
HTTP 狀態碼 200 表示已經接受要求且正在處理。 這表示作業已順利完成。

下表列出服務可能傳回的一整組狀態碼︰

| 程式碼 | 狀態 | 錯誤碼 | 描述 |
|:--- |:--- |:--- |:--- |
| 200 |[確定] | |已順利接受要求。 |
| 400 |不正確的要求 |InactiveCustomer |已關閉工作區。 |
| 400 |不正確的要求 |InvalidApiVersion |您所指定但服務無法辨識的 API 版本。 |
| 400 |不正確的要求 |InvalidCustomerId |指定的工作區識別碼無效。 |
| 400 |不正確的要求 |InvalidDataFormat |提交的 JSON 無效。 回應主體可能包含有關如何解決錯誤的詳細資訊。 |
| 400 |不正確的要求 |InvalidLogType |指定的記錄檔類型包含特殊字元或數值。 |
| 400 |不正確的要求 |MissingApiVersion |未指定 API 版本。 |
| 400 |不正確的要求 |MissingContentType |未指定內容類型。 |
| 400 |不正確的要求 |MissingLogType |未指定必要值的記錄檔類型。 |
| 400 |不正確的要求 |UnsupportedContentType |內容類型未設定為 [應用程式/json]。 |
| 403 |禁止 |InvalidAuthorization |服務無法驗證要求。 請確認工作區識別碼和連線金鑰都正確。 |
| 404 |找不到 | | 提供的 URL 不正確，或是要求是太大。 |
| 429 |太多要求 | | 服務遭遇大量資料來自您的帳戶。 請稍後再重試要求。 |
| 500 |內部伺服器錯誤 |UnspecifiedError |服務發生內部錯誤。 請重試要求。 |
| 503 |服務無法使用 |ServiceUnavailable |服務目前無法用來接收要求。 請重試您的要求。 |

## <a name="query-data"></a>查詢資料
若要查詢 Azure 監視器 HTTP 資料收集器 API 所提交的資料，請搜尋 **Type** 等於您所指定之 **LogType** 值且附加 **_CL** 的記錄。 例如，如果您使用 **MyCustomLog**，則會傳回 `MyCustomLog_CL` 的所有記錄。

## <a name="sample-requests"></a>範例要求
在後續各節中，您會找到如何使用不同的程式設計語言，將資料提交至 Azure 監視器 HTTP 資料收集器 API 的範例。

在每個範例中，執行下列步驟來設定授權標頭的變數︰

1. 在 Azure 入口網站中，找出 Log Analytics 工作區。
2. 選取 [ **代理程式管理**]。
2. 選取 [工作區識別碼] 右邊的複製圖示，然後貼上識別碼做為 [客戶識別碼] 變數值。
3. 選取 [主要金鑰] 右邊的複製圖示，然後貼上識別碼做為 [共用金鑰] 變數值。

或者，您可以變更記錄檔類型和 JSON 資料的變數。

### <a name="powershell-sample"></a>PowerShell 範例
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C# 範例
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Python 2 範例
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

### <a name="python-3-sample"></a>Python 3 範例
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash, encoding="utf-8")  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest()).decode()
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print('Accepted')
    else:
        print("Response code: {}".format(response.status_code))

post_data(customer_id, shared_key, body, log_type)
```


### <a name="java-sample"></a>Java 範例

```java

import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.springframework.http.MediaType;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.text.SimpleDateFormat;
import java.util.Base64;
import java.util.Calendar;
import java.util.TimeZone;

import static org.springframework.http.HttpHeaders.CONTENT_TYPE;

public class ApiExample {

  private static final String workspaceId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
  private static final String sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";
  private static final String logName = "DemoExample";
  /*
  You can use an optional field to specify the timestamp from the data. If the time field is not specified,
  Azure Monitor assumes the time is the message ingestion time
   */
  private static final String timestamp = "";
  private static final String json = "{\"name\": \"test\",\n" + "  \"id\": 1\n" + "}";
  private static final String RFC_1123_DATE = "EEE, dd MMM yyyy HH:mm:ss z";

  public static void main(String[] args) throws IOException, NoSuchAlgorithmException, InvalidKeyException {
    String dateString = getServerTime();
    String httpMethod = "POST";
    String contentType = "application/json";
    String xmsDate = "x-ms-date:" + dateString;
    String resource = "/api/logs";
    String stringToHash = String
        .join("\n", httpMethod, String.valueOf(json.getBytes(StandardCharsets.UTF_8).length), contentType,
            xmsDate , resource);
    String hashedString = getHMAC254(stringToHash, sharedKey);
    String signature = "SharedKey " + workspaceId + ":" + hashedString;

    postData(signature, dateString, json);
  }

  private static String getServerTime() {
    Calendar calendar = Calendar.getInstance();
    SimpleDateFormat dateFormat = new SimpleDateFormat(RFC_1123_DATE);
    dateFormat.setTimeZone(TimeZone.getTimeZone("GMT"));
    return dateFormat.format(calendar.getTime());
  }

  private static void postData(String signature, String dateString, String json) throws IOException {
    String url = "https://" + workspaceId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
    HttpPost httpPost = new HttpPost(url);
    httpPost.setHeader("Authorization", signature);
    httpPost.setHeader(CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE);
    httpPost.setHeader("Log-Type", logName);
    httpPost.setHeader("x-ms-date", dateString);
    httpPost.setHeader("time-generated-field", timestamp);
    httpPost.setEntity(new StringEntity(json));
    try(CloseableHttpClient httpClient = HttpClients.createDefault()){
      HttpResponse response = httpClient.execute(httpPost);
      int statusCode = response.getStatusLine().getStatusCode();
      System.out.println("Status code: " + statusCode);
    }
  }

  private static String getHMAC254(String input, String key) throws InvalidKeyException, NoSuchAlgorithmException {
    String hash;
    Mac sha254HMAC = Mac.getInstance("HmacSHA256");
    Base64.Decoder decoder = Base64.getDecoder();
    SecretKeySpec secretKey = new SecretKeySpec(decoder.decode(key.getBytes(StandardCharsets.UTF_8)), "HmacSHA256");
    sha254HMAC.init(secretKey);
    Base64.Encoder encoder = Base64.getEncoder();
    hash = new String(encoder.encode(sha254HMAC.doFinal(input.getBytes(StandardCharsets.UTF_8))));
    return hash;
  }

}


```


## <a name="alternatives-and-considerations"></a>替代方案和考慮
雖然資料收集器 API 應該涵蓋將自由格式資料收集到 Azure 記錄檔的大部分需求，但是有一些可能需要替代方法來克服 API 限制的情況。 您所有的選項如下所示，主要考慮如下：

| 替代函式 | 描述 | 最適用於 |
|---|---|---|
| [自訂事件](../app/api-custom-events-metrics.md?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties)： Application Insights 中以原生 SDK 為基礎的內嵌 | Application Insights （通常是透過應用程式內的 SDK 進行檢測）可讓您透過自訂事件傳送自訂資料。 | <ul><li> 在您的應用程式內產生的資料，但是 SDK 無法透過其中一種預設資料類型 (要求、相依性、例外狀況等) 來挑選資料。</li><li> 最常與 Application Insights 中的其他應用程式資料相關聯的資料 </li></ul> |
| Azure 監視器記錄中的資料收集器 API | Azure 監視器記錄檔中的資料收集器 API 是完全以開放式方式內嵌資料的方式。 JSON 物件中格式化的任何資料都可以在這裡傳送。 一旦傳送之後，它就會被處理，並可在記錄檔中使用，以與記錄中的其他資料或其他 Application Insights 資料相互關聯。 <br/><br/> 將資料以檔案形式上傳至 Azure Blob blob 相當容易，因為這些檔案將在其中處理並上傳至 Log Analytics。 請參閱 [這](./create-pipeline-datacollector-api.md) 篇文章，以取得這類管線的範例執行。 | <ul><li> 不一定會在 Application Insights 中檢測的應用程式內產生的資料。</li><li> 範例包括查閱和事實資料表、參考資料、預先匯總統計資料等等。 </li><li> 適用于將針對其他 Azure 監視器資料進行交叉參照的資料 (Application Insights、其他記錄資料類型、資訊安全中心、容器/Vm Azure 監視器等，) 依此類推。 </li></ul> |
| [Azure 資料總管](/azure/data-explorer/ingest-data-overview) | Azure 資料總管 (ADX) 是可 Application Insights 分析和 Azure 監視器記錄的資料平臺。 現已正式推出 ( 「GA」 ) ，在其原始形式中使用資料平臺可提供您完整的彈性 (但需要在叢集上進行管理) 的額外負荷 (Kubernetes RBAC、保留費率、架構等) 。 ADX 提供許多內嵌 [選項](/azure/data-explorer/ingest-data-overview#ingestion-methods) ，包括 [CSV、TSV 和 JSON](/azure/kusto/management/mappings?branch=master) 檔案。 | <ul><li> 將不會與 Application Insights 或記錄下的任何其他資料相互關聯的資料。 </li><li> Azure 監視器記錄中目前無法使用先進的內嵌或處理功能的資料。 </li></ul> |


## <a name="next-steps"></a>後續步驟
- 使用[記錄搜尋 API](../log-query/log-query-overview.md) 從 Log Analytics 工作區擷取資料。

- 深入了解如何透過 Logic Apps 工作流程，[使用資料收集器 API 建立通往 Azure 監視器的資料管線](create-pipeline-datacollector-api.md)。