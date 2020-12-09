---
title: Azure 應用程式組態 REST API-HMAC 驗證
description: 使用 HMAC 向 Azure 應用程式組態驗證，方法是使用 REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: f761d86bd7dbe54b687e8db75ecb3df2dbba3384
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932705"
---
# <a name="hmac-authentication---rest-api-reference"></a>HMAC 驗證-REST API 參考

您可以使用 HMAC-SHA256 驗證配置來驗證 HTTP 要求。  (HMAC 指的是以雜湊為基礎的消息驗證碼。 ) 這些要求必須透過 TLS 傳輸。

## <a name="prerequisites"></a>必要條件

- **憑據** - \<Access Key ID\>
- **Secret** -base64 解碼的存取金鑰值。 ``base64_decode(<Access Key Value>)``

認證的值 (也稱為 `id`) 和秘密 (也稱為 `value`) 必須從 Azure 應用程式組態的實例取得。 您可以使用 [Azure 入口網站](https://portal.azure.com) 或 [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest)來完成這項作業。

為每個要求提供驗證所需的所有 HTTP 標頭。 所需的最小值為：

|  要求標頭 | 描述  |
| --------------- | ------------ |
| **主控件** | 網際網路主機和埠號碼。 如需詳細資訊，請參閱  [3.2.2](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.2.2)一節。 |
| **日期** | 發出要求的日期和時間。 目前的國際標準時間不能超過15分鐘的時間， (格林尼治平均時間) 。 此值為 HTTP 日期，如 [3.3.1](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.3.1)一節中所述。
| **x-ms-date** | 與 ```Date``` 上述相同。 您可以使用它，而不是在代理程式無法直接存取 ```Date``` 要求標頭時使用，或由 proxy 修改它。 如果 ```x-ms-date``` ```Date``` 同時提供了和，則 ```x-ms-date``` 會優先使用。 |
| **x-ms-內容-sha256** | base64 編碼的要求本文的 SHA256 雜湊。 即使沒有主體，也必須提供。 ```base64_encode(SHA256(body))```|
| **授權** | HMAC-SHA256 配置所需的驗證資訊。 本文稍後會說明格式和詳細資料。 |

**範例︰**

```http
Host: {myconfig}.azconfig.io
Date: Fri, 11 May 2018 18:48:36 GMT
x-ms-content-sha256: {SHA256 hash of the request body}
Authorization: HMAC-SHA256 Credential={Access Key ID}&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature={Signature}
```

## <a name="authorization-header"></a>驗證標頭

### <a name="syntax"></a>語法

``Authorization``： **HMAC-SHA256**```Credential```=\<value\>&```SignedHeaders```=\<value\>&```Signature```=\<value\>

|  引數 | 描述  |
| ------ | ------ |
| **HMAC-SHA256** | 授權配置。 _需要 ()_ |
| **認證** | 用來計算簽章的存取金鑰識別碼。 _需要 ()_ |
| **SignedHeaders** | 加入至簽章的 HTTP 要求標頭。 _需要 ()_ |
| **簽章** | 以 base64 編碼的字串對符號 HMACSHA256。 _需要 ()_|

### <a name="credential"></a>認證

用來計算簽章的存取金鑰識別碼。

### <a name="signed-headers"></a>帶正負號的標頭

簽署要求所需的 HTTP 要求標頭名稱（以分號分隔）。 要求也必須正確提供這些 HTTP 標頭。 請勿使用空白字元。

### <a name="required-http-request-headers"></a>必要的 HTTP 要求標頭

```x-ms-date```[或 ```Date``` ]; ```host``` ;```x-ms-content-sha256```

任何其他 HTTP 要求標頭也可以新增至簽署。 只需將它們附加至 ```SignedHeaders``` 引數即可。

**範例︰**

x-ms-日; 主機; x-ms-內容-sha256; ```Content-Type``` ;```Accept```

### <a name="signature"></a>簽章

以 Base64 編碼的字串對符號 HMACSHA256 雜湊。 它會使用所識別的存取金鑰 `Credential` 。
```base64_encode(HMACSHA256(String-To-Sign, Secret))```

### <a name="string-to-sign"></a>字串對符號

這是要求的標準標記法：

_字串至符號 =_

**HTTP_METHOD** + ' \n ' + **path_and_query** + ' \n ' + **signed_headers_values**

|  引數 | 描述  |
| ------ | ------ |
| **HTTP_METHOD** | 與要求搭配使用的大寫 HTTP 方法名稱。 如需詳細資訊，請參閱 [第9節](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。 |
|**path_and_query** | 要求絕對 URI 路徑和查詢字串的串連。 如需詳細資訊，請參閱 [第3.3 節](https://tools.ietf.org/html/rfc3986#section-3.3)。
| **signed_headers_values** | 中所列之所有 HTTP 要求標頭的分號分隔值 `SignedHeaders` 。 格式如下： `SignedHeaders` 語義。 |

**範例︰**

```js
string-To-Sign=
            "GET" + '\n' +                                                                                  // VERB
            "/kv?fields=*&api-version=1.0" + '\n' +                                                         // path_and_query
            "Fri, 11 May 2018 18:48:36 GMT;{myconfig}.azconfig.io;{value of ms-content-sha256 header}"      // signed_headers_values
```


### <a name="errors"></a>Errors

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**原因：** 未提供具有 HMAC-SHA256 配置的授權要求標頭。

**解決方案：** 請提供有效的 ```Authorization``` HTTP 要求標頭。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="The access token has expired", Bearer
```

**原因：** ```Date``` 或 ```x-ms-date``` 要求標頭超過了15分鐘的時間，從目前的國際標準時間 (格林尼治平均時間) 。

**解決方案：** 提供正確的日期和時間。


```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid access token date", Bearer
```

**原因：** 遺漏或無效 ```Date``` 或 ```x-ms-date``` 要求標頭。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="[Credential][SignedHeaders][Signature] is required", Bearer
```

**原因：** 要求標頭中缺少必要的參數 ```Authorization``` 。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Credential", Bearer
```

**原因：** 找不到提供的 [ ```Host``` ]/[存取金鑰識別碼]。

**解決方案：** 檢查 ```Credential``` ```Authorization``` 要求標頭的參數。 請確定它是有效的存取金鑰識別碼，並確定 ```Host``` 標頭指向已註冊的帳戶。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature", Bearer
```

**原因：**```Signature```提供的不符合伺服器預期的。

**解決方案：** 請確定 ```String-To-Sign``` 正確無誤。 ```Secret```使用) 之前，請確定 (base64 已解碼正確且正確地使用。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Signed request header 'xxx' is not provided", Bearer
```

**原因：** 標頭中的參數遺漏要求標頭 ```SignedHeaders```  ```Authorization``` 。

**解決方案：** 提供必要的標頭，並提供正確的值。

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="XXX is required as a signed header", Bearer
```

**原因：** 中缺少參數 ```SignedHeaders``` 。

**解決方案：** 檢查已簽署的標頭最低需求。

## <a name="code-snippets"></a>程式碼片段

### <a name="javascript"></a>JavaScript

*必要條件*： [加密-JS](https://code.google.com/archive/p/crypto-js/)

```js
function signRequest(host, 
                     method,      // GET, PUT, POST, DELETE
                     url,         // path+query
                     body,        // request body (undefined of none)
                     credential,  // access key id
                     secret)      // access key value (base64 encoded)
{
        var verb = method.toUpperCase();
        var utcNow = new Date().toUTCString();
        var contentHash = CryptoJS.SHA256(body).toString(CryptoJS.enc.Base64);

        //
        // SignedHeaders
        var signedHeaders = "x-ms-date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign = 
            verb + '\n' +                              // VERB
            url + '\n' +                               // path_and_query
            utcNow + ';' + host + ';' + contentHash;   // Semicolon separated SignedHeaders values

        //
        // Signature
        var signature = CryptoJS.HmacSHA256(stringToSign, CryptoJS.enc.Base64.parse(secret)).toString(CryptoJS.enc.Base64);

        //
        // Result request headers
        return [
            { name: "x-ms-date", value: utcNow },
            { name: "x-ms-content-sha256", value: contentHash },
            { name: "Authorization", value: "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signedHeaders + "&Signature=" + signature }
        ];
}
```

### <a name="c"></a>C#

```csharp
using (var client = new HttpClient())
{
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://{config store name}.azconfig.io/kv?api-version=1.0"),
        Method = HttpMethod.Get
    };

    //
    // Sign the request
    request.Sign(<Credential>, <Secret>);

    await client.SendAsync(request);
}

static class HttpRequestMessageExtensions
{
    public static HttpRequestMessage Sign(this HttpRequestMessage request, string credential, byte[] secret)
    {
        string host = request.RequestUri.Authority;
        string verb = request.Method.ToString().ToUpper();
        DateTimeOffset utcNow = DateTimeOffset.UtcNow;
        string contentHash = Convert.ToBase64String(request.Content.ComputeSha256Hash());

        //
        // SignedHeaders
        string signedHeaders = "date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign = $"{verb}\n{request.RequestUri.PathAndQuery}\n{utcNow.ToString("r")};{host};{contentHash}";

        //
        // Signature
        string signature;

        using (var hmac = new HMACSHA256(secret))
        {
            signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign)));
        }

        //
        // Add headers
        request.Headers.Date = utcNow;
        request.Headers.Add("x-ms-content-sha256", contentHash);
        request.Headers.Authorization = new AuthenticationHeaderValue("HMAC-SHA256", $"Credential={credential}&SignedHeaders={signedHeaders}&Signature={signature}");

        return request;
    }
}

static class HttpContentExtensions
{
    public static byte[] ComputeSha256Hash(this HttpContent content)
    {
        using (var stream = new MemoryStream())
        {
            if (content != null)
            {
                content.CopyToAsync(stream).Wait();
                stream.Seek(0, SeekOrigin.Begin);
            }

            using (var alg = SHA256.Create())
            {
                return alg.ComputeHash(stream.ToArray());
            }
        }
    }
}
```

### <a name="java"></a>Java

```java
public CloseableHttpResponse signRequest(HttpUriRequest request, String credential, String secret)
        throws IOException, URISyntaxException {
    Map<String, String> authHeaders = generateHeader(request, credential, secret);
    authHeaders.forEach(request::setHeader);

    return httpClient.execute(request);
}

private static Map<String, String> generateHeader(HttpUriRequest request, String credential, String secret) 
        throws URISyntaxException, IOException {
    String requestTime = GMT_DATE_FORMAT.format(new Date());

    String contentHash = buildContentHash(request);
    // SignedHeaders
    String signedHeaders = "x-ms-date;host;x-ms-content-sha256";

    // Signature
    String methodName = request.getRequestLine().getMethod().toUpperCase();
    URIBuilder uri = new URIBuilder(request.getRequestLine().getUri());
    String scheme = uri.getScheme() + "://";
    String requestPath = uri.toString().substring(scheme.length()).substring(uri.getHost().length());
    String host = new URIBuilder(request.getRequestLine().getUri()).getHost();
    String toSign = String.format("%s\n%s\n%s;%s;%s", methodName, requestPath, requestTime, host, contentHash);

    byte[] decodedKey = Base64.getDecoder().decode(secret);
    String signature = Base64.getEncoder().encodeToString(new HmacUtils(HMAC_SHA_256, decodedKey).hmac(toSign));

    // Compose headers
    Map<String, String> headers = new HashMap<>();
    headers.put("x-ms-date", requestTime);
    headers.put("x-ms-content-sha256", contentHash);

    String authorization = String.format("HMAC-SHA256 Credential=%s, SignedHeaders=%s, Signature=%s",
            credential, signedHeaders, signature);
    headers.put("Authorization", authorization);

    return headers;
}

private static String buildContentHash(HttpUriRequest request) throws IOException {
    String content = "";
    if (request instanceof HttpEntityEnclosingRequest) {
        try {
            StringWriter writer = new StringWriter();
            IOUtils.copy(((HttpEntityEnclosingRequest) request).getEntity().getContent(), writer,
                    StandardCharsets.UTF_8);

            content = writer.toString();
        }
        finally {
            ((HttpEntityEnclosingRequest) request).getEntity().getContent().close();
        }
    }

    byte[] digest = new DigestUtils(SHA_256).digest(content);
    return Base64.getEncoder().encodeToString(digest);
}
```

### <a name="golang"></a>Golang

```golang
import (
    "bytes"
    "crypto/hmac"
    "crypto/sha256"
    "encoding/base64"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

//SignRequest Setup the auth header for accessing Azure App Configuration service
func SignRequest(id string, secret string, req *http.Request) error {
    method := req.Method
    host := req.URL.Host
    pathAndQuery := req.URL.Path
    if req.URL.RawQuery != "" {
        pathAndQuery = pathAndQuery + "?" + req.URL.RawQuery
    }

    content, err := ioutil.ReadAll(req.Body)
    if err != nil {
        return err
    }
    req.Body = ioutil.NopCloser(bytes.NewBuffer(content))

    key, err := base64.StdEncoding.DecodeString(secret)
    if err != nil {
        return err
    }

    timestamp := time.Now().UTC().Format(http.TimeFormat)
    contentHash := getContentHashBase64(content)
    stringToSign := fmt.Sprintf("%s\n%s\n%s;%s;%s", strings.ToUpper(method), pathAndQuery, timestamp, host, contentHash)
    signature := getHmac(stringToSign, key)

    req.Header.Set("x-ms-content-sha256", contentHash)
    req.Header.Set("x-ms-date", timestamp)
    req.Header.Set("Authorization", "HMAC-SHA256 Credential="+id+", SignedHeaders=x-ms-date;host;x-ms-content-sha256, Signature="+signature)

    return nil
}

func getContentHashBase64(content []byte) string {
    hasher := sha256.New()
    hasher.Write(content)
    return base64.StdEncoding.EncodeToString(hasher.Sum(nil))
}

func getHmac(content string, key []byte) string {
    hmac := hmac.New(sha256.New, key)
    hmac.Write([]byte(content))
    return base64.StdEncoding.EncodeToString(hmac.Sum(nil))
}
```

### <a name="python"></a>Python

```python

import base64
import hashlib
import hmac
from datetime import datetime
import six

def sign_request(host,
                method,     # GET, PUT, POST, DELETE
                url,        # Path + Query
                body,       # Request body 
                credential, # Access Key ID
                secret):    # Access Key Value
    verb = method.upper()

    utc_now = str(datetime.utcnow().strftime("%b, %d %Y %H:%M:%S ")) + "GMT"

    if six.PY2:
        content_digest = hashlib.sha256(bytes(body)).digest()
    else:
        content_digest = hashlib.sha256(bytes(body, 'utf-8')).digest()

    content_hash = base64.b64encode(content_digest).decode('utf-8')

    # Signed Headers
    signed_headers = "x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names

    # String-To-Sign
    string_to_sign = verb + '\n' + \
                    url + '\n' + \
                    utc_now + ';' + host + ';' + content_hash  # Semicolon separated SignedHeaders values

    # Decode secret
    if six.PY2:
        decoded_secret = base64.b64decode(secret)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign), hashlib.sha256).digest()
    else:
        decoded_secret = base64.b64decode(secret, validate=True)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign, 'utf-8'), hashlib.sha256).digest()

    # Signature
    signature = base64.b64encode(digest).decode('utf-8')

    # Result request headers
    return {
        "x-ms-date": utc_now,
        "x-ms-content-sha256": content_hash,
        "Authorization": "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signed_headers + "&Signature=" + signature
    }
```

### <a name="powershell"></a>PowerShell

```PowerShell
function Sign-Request(
    [string] $hostname,
    [string] $method,      # GET, PUT, POST, DELETE
    [string] $url,         # path+query
    [string] $body,        # request body
    [string] $credential,  # access key id
    [string] $secret       # access key value (base64 encoded)
)
{  
    $verb = $method.ToUpperInvariant()
    $utcNow = (Get-Date).ToUniversalTime().ToString("R", [Globalization.DateTimeFormatInfo]::InvariantInfo)
    $contentHash = Compute-SHA256Hash $body

    $signedHeaders = "x-ms-date;host;x-ms-content-sha256";  # Semicolon separated header names

    $stringToSign = $verb + "`n" +
                    $url + "`n" +
                    $utcNow + ";" + $hostname + ";" + $contentHash  # Semicolon separated signedHeaders values

    $signature = Compute-HMACSHA256Hash $secret $stringToSign

    # Return request headers
    return @{
        "x-ms-date" = $utcNow;
        "x-ms-content-sha256" = $contentHash;
        "Authorization" = "HMAC-SHA256 Credential=" + $credential + "&SignedHeaders=" + $signedHeaders + "&Signature=" + $signature
    }
}

function Compute-SHA256Hash(
    [string] $content
)
{
    $sha256 = [System.Security.Cryptography.SHA256]::Create()
    try {
        return [Convert]::ToBase64String($sha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $sha256.Dispose()
    }
}

function Compute-HMACSHA256Hash(
    [string] $secret,      # base64 encoded
    [string] $content
)
{
    $hmac = [System.Security.Cryptography.HMACSHA256]::new([Convert]::FromBase64String($secret))
    try {
        return [Convert]::ToBase64String($hmac.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $hmac.Dispose()
    }
}

# Stop if any error occurs
$ErrorActionPreference = "Stop"

$uri = [System.Uri]::new("https://{myconfig}.azconfig.io/kv?api-version=1.0")
$method = "GET"
$body = $null
$credential = "<Credential>"
$secret = "<Secret>"

$headers = Sign-Request $uri.Authority $method $uri.PathAndQuery $body $credential $secret
Invoke-RestMethod -Uri $uri -Method $method -Headers $headers -Body $body
```

### <a name="bash"></a>Bash

*必要條件*：

| 必要條件 | 命令 | 測試的版本 |
| ------------ | ------- | --------------- |
| [Bash](https://www.gnu.org/software/bash/) | Bash | 3.5.27, 4.4.23 |
| [coreutils](https://www.gnu.org/software/coreutils/) | tr | 8.28 |
| [curl](https://curl.haxx.se/) | curl | 7.55.1, 7.58.0 |
| [Openssl](https://www.openssl.org/) | openssl | 1.1.0 g、1.1.1 a |
| [util-linux](https://github.com/karelzak/util-linux/) | hexdump | 2.14.1, 2.31.1 |

```bash
#!/bin/bash

sign_request () {
    local host="$1"
    local method="$2"      # GET, PUT, POST, DELETE
    local url="$3"         # path+query
    local body="$4"        # request body
    local credential="$5"  # access key id
    local secret="$6"      # access key value (base64 encoded)

    local verb=$(printf "$method" | tr '[:lower:]' '[:upper:]')
    local utc_now="$(date -u '+%a, %d %b %Y %H:%M:%S GMT')"
    local content_hash="$(printf "$body" | openssl sha256 -binary | base64)"

    local signed_headers="x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names
    local string_to_sign="$verb\n$url\n$utc_now;$host;$content_hash"  # Semicolon separated signed_headers values

    local decoded_secret="$(printf "$secret" | base64 -d | hexdump -v -e '/1 "%02x"')"
    local signature="$(printf "$string_to_sign" | openssl sha256 -mac HMAC -macopt hexkey:"$decoded_secret" -binary | base64)"

    # Output request headers
    printf '%s\n' \
           "x-ms-date: $utc_now" \
           "x-ms-content-sha256: $content_hash" \
           "Authorization: HMAC-SHA256 Credential=$credential&SignedHeaders=$signed_headers&Signature=$signature"
}

host="{config store name}.azconfig.io"
method="GET"
url="/kv?api-version=1.0"
body=""
credential="<Credential>"
secret="<Secret>"

headers=$(sign_request "$host" "$method" "$url" "$body" "$credential" "$secret")

while IFS= read -r line; do
    header_args+=("-H$line")
done <<< "$headers"
curl -X "$method" -d "$body" "${header_args[@]}" "https://$host$url"
```