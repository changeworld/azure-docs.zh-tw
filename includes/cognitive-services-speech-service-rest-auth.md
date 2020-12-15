---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 410b73d3f9011f9384fafa18394d9318e3eafa67
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504486"
---
## <a name="authentication"></a>驗證

每個要求都需要授權標頭。 下表會列出各項服務支援的標頭：

| 支援的授權標頭 | 語音轉文字 | 文字轉換語音 |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | 是 | 否 |
| 授權：持有人 | 是 | 是 |

當使用 `Ocp-Apim-Subscription-Key` 標頭，只需要提供您的訂用帳戶金鑰。 例如：

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

使用 `Authorization: Bearer` 標頭時，需要對 `issueToken` 端點提出要求。 在此要求中，要以訂用帳戶金鑰交換有效期間 10 分鐘的存取權杖。 在接下來的幾節中，您將瞭解如何取得權杖，並使用權杖。

### <a name="how-to-get-an-access-token"></a>如何取得存取權杖

若要取得存取權杖，您必須使用 `Ocp-Apim-Subscription-Key` 和您的訂用帳戶金鑰，對 `issueToken` 端點提出要求。

`issueToken`端點的格式如下：

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

取代 `<REGION_IDENTIFIER>` 為與此資料表中的訂用帳戶區域相符的識別碼：

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

使用以下範例建立您的存取權杖要求。

#### <a name="http-sample"></a>HTTP 範例

這個範例是可取得權杖的簡單 HTTP 要求。 使用您的語音服務訂用帳戶金鑰來取代 `YOUR_SUBSCRIPTION_KEY`。 如果您的訂用帳戶不在美國西部區域，請以您區域的主機名稱取代 `Host`。

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

回應的本文包含採用 JSON Web 權杖 (JWT) 格式的存取權杖。

#### <a name="powershell-sample"></a>PowerShell 範例

這個範例是簡單的 PowerShell 指令碼，用來取得存取權杖。 使用您的語音服務訂用帳戶金鑰來取代 `YOUR_SUBSCRIPTION_KEY`。 請務必使用符合您訂用帳戶區域的正確端點。 此範例目前設為「美國西部」。

```powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>cURL 範例

cURL 是 Linux (以及適用於 Linux 的 Windows 子系統) 中可用的命令列工具。 此 cURL 命令說明如何取得存取權杖。 使用您的語音服務訂用帳戶金鑰來取代 `YOUR_SUBSCRIPTION_KEY`。 請務必使用符合您訂用帳戶區域的正確端點。 此範例目前設為「美國西部」。

```console
curl -v -X POST \
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C# 範例

此 C# 類別說明了如何取得存取權杖。 在您具現化類別時，請傳遞您的語音服務訂用帳戶金鑰。 如果您的訂用帳戶不在美國西部區域，請變更 `FetchTokenUri` 的值以符合您訂用帳戶的區域。

```csharp
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

#### <a name="python-sample"></a>Python 範例

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>如何使用存取權杖

存取權杖應傳送到服務作為 `Authorization: Bearer <TOKEN>` 標頭。 每一個存取權杖的有效時間為 10 分鐘。 您可以隨時取得新權杖，但為了盡量降低網路流量和延遲，建議您使用相同的權杖九分鐘。

以下是簡短音訊語音轉換文字 REST API 的 HTTP 要求範例：

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
