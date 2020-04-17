---
title: 將託管識別與應用程式一起使用
description: 如何在 Azure 服務結構應用程式代碼中使用託管標識來造訪 Azure 服務。
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: 8f1f355d6add16f3b3ec25bc569f9b198a8d6778
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461560"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>如何利用服務結構應用程式的託管識別存取 Azure 服務

服務結構應用程式可以利用託管標識存取支援基於 Azure 活動目錄身份驗證的其他 Azure 資源。 應用程式可以獲取表示其標識(可以是系統分配或使用者分配的[)的訪問令牌](../active-directory/develop/developer-glossary.md#access-token),並將其用作"承載"令牌,以向另一個服務(也稱為[受保護資源伺服器](../active-directory/develop/developer-glossary.md#resource-server))進行身份驗證。 令牌表示分配給 Service Fabric 應用程式的標識,將僅頒發給共用該標識的 Azure 資源(包括 SF 應用程式)。 有關託管標識的詳細說明以及系統分配和使用者分配標識之間的區別,請參閱[託管標識概述](../active-directory/managed-identities-azure-resources/overview.md)文檔。 在本文中,我們將將啟用託管識別的服務結構應用程式稱為[客戶端應用程式](../active-directory/develop/developer-glossary.md#client-application)。

> [!IMPORTANT]
> 託管標識表示與包含資源的訂閱關聯的相應 Azure AD 租戶中的 Azure 資源和服務主體之間的關聯。 因此,在 Service Fabric 的上下文中,託管標識僅支援作為 Azure 資源部署的應用程式。 

> [!IMPORTANT]
> 在使用 Service Fabric 應用程式的託管標識之前,必須授予用戶端應用程式對受保護資源的訪問。 請參閱[支援 Azure AD 身份驗證](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)以檢查支援的 Azure 服務清單,然後參閱相應服務的文件,瞭解授予對感興趣的資源的身份訪問許可權的特定步驟。 

## <a name="acquiring-an-access-token-using-rest-api"></a>使用 REST API 取得存取權杖
在為託管標識啟用的群集中,Service Fabric 運行時公開了本地主機終結點,應用程式可以使用該終結點獲取訪問令牌。 終結點在群集的每個節點上可用,並且該節點上的所有實體都可以訪問該終結點。 授權調用方可以通過調用此終結點並顯示身份驗證代碼來獲取訪問令牌;代碼由服務結構運行時為每個不同的服務代碼包啟動生成,並綁定到託管該服務代碼包的進程的存留期。

具體而言,啟用託管標識的服務交換矩陣服務的環境將包含以下變數:
- "IDENTITY_ENDPOINT":與服務的託管標識對應的本地主機終結點
- "IDENTITY_HEADER":表示當前節點上服務的唯一身份驗證代碼
- "IDENTITY_SERVER_THUMBPRINT":服務結構託管標識伺服器的指紋

> [!IMPORTANT]
> 應用程式代碼應將「IDENTITY_HEADER」環境變數的值視為敏感數據 - 不應記錄或以其他方式傳播該變數。 身份驗證代碼在本地節點之外或託管服務的進程終止後沒有值,但它確實表示服務交換矩陣服務的標識,因此應使用與訪問權杖本身相同的預防措施來處理。

要取得權杖,用戶端執行以下步驟:
- 通過將託管識別終結點(IDENTITY_ENDPOINT值)與 API 版本和權杖所需的資源(存取群體)串聯,形成 URI
- 指定的 URI 建立 GET HTTP(s) 要求
- 新增適當的伺服器憑證認證邏輯
- 將驗證代碼 (IDENTITY_HEADER值) 作為標頭加入到請求
- 提交要求

成功的回應將包含表示結果訪問令牌的 JSON 負載,以及描述它的元數據。 失敗的回應還包括對失敗的解釋。 有關錯誤處理的其他詳細資訊,請參閱下文。

訪問權杖將由 Service Fabric 快取到各個等級(節點、叢集、資源提供程式服務),因此成功的回應並不一定意味著權杖是直接針對使用者應用程式的請求發出的。 令牌的緩存時間將小於其生存期,因此保證應用程式接收有效的權杖。 建議應用程式代碼自行緩存它獲取的任何訪問權杖;緩存鍵應包括(派生)訪問群體。 

範例要求：
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
其中：

| 項目 | 描述 |
| ------- | ----------- |
| `GET` | HTTP 指令動詞，指出您想要擷取端點中的資料。 在此案例中是 OAuth 存取權杖。 | 
| `https://localhost:2377/metadata/identity/oauth2/token` | 通過IDENTITY_ENDPOINT環境變數提供的 Service Fabric 應用程式的託管標識終結點。 |
| `api-version` | 查詢字串參數,指定託管標識令牌服務的 API 版本;目前唯一接受的值是`2019-07-01-preview`,並且可能會更改。 |
| `resource` | 查詢字串參數，指出目標資源的應用程式識別碼 URI。 這將反映為已頒發的權杖`aud`的(受眾)聲明。 本示例請求權杖存取 Azure 密鑰保管庫,其應用\/IDURI 是 Hs: /vault.azure.net/。 |
| `Secret` | 服務結構服務的服務結構託管標識權杖服務需要一個 HTTP 請求標頭欄位,用於對調用方進行身份驗證。 此值由 SF 運行時透過IDENTITY_HEADER環境變數提供。 |


範例回應：
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
其中：

| 項目 | 描述 |
| ------- | ----------- |
| `token_type` | 令牌的類型;在這種情況下,一個"承載者"訪問權杖,這意味著此令牌的演示者("持有者")是令牌的預期主題。 |
| `access_token` | 所要求的存取權杖。 呼叫受保護的 REST API 時，權杖會內嵌在 `Authorization` 要求標頭欄位中成為「持有人」權杖，以允許 API 驗證呼叫端。 | 
| `expires_on` | 訪問權杖過期的時間戳;表示為「1970-01-01T0:0:0Z UTC」的秒數,並對應於權杖的`exp`聲明 。 在這種情況下,令牌過期於 2019-08-08T06:10:11_00:00 (在 RFC 3339)|
| `resource` | 發出訪問權杖的資源,通過請求的`resource`查詢字串參數指定;對應於權杖的"aud"聲明。 |


## <a name="acquiring-an-access-token-using-c"></a>使用 C 取得存取權杖#
上述情況在 C# 中變為:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>使用託管識別從服務結構應用程式存取金鑰保管庫
此示例基於上述範例,以演示使用託管標識存取存儲在密鑰保管庫中的機密。

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>錯誤處理
HTTP 回應標頭的「狀態代碼」欄位指示請求的成功狀態;"200 OK"狀態表示成功,回應將包括上述訪問令牌。 以下是可能的錯誤回應的簡短枚舉。

| 狀態碼 | 錯誤原因 | 處理方式 |
| ----------- | ------------ | ------------- |
| 404 找不到。 | 身份驗證代碼未知,或者未為應用程式分配託管標識。 | 更正應用程式設定或令牌獲取代碼。 |
| 429 要求太多。 |  達到的油門限制,由 AAD 或 SF 施加。 | 使用指數輪詢重試。 請參閱下面的指引。 |
| 要求中的 4xx 錯誤。 | 一個或多個要求參數不正確。 | 請勿重試。  檢查錯誤詳細資料以取得更多資訊。  4xx 錯誤是設計階段錯誤。|
| 5xx 服務錯誤。 | 託管識別子系統或 Azure 活動目錄返回暫時性錯誤。 | 一會兒重試是安全的。 重試時,可能會達到限制條件 (429)。|

如果發生錯誤,相應的 HTTP 回應正文包含 JSON 物件,其中包含錯誤詳細資訊:

| 項目 | 描述 |
| ------- | ----------- |
| 代碼 | 錯誤碼。 |
| correlationId | 可用於調試的相關 ID。 |
| message | 錯誤的詳細資訊描述。 **錯誤描述可以隨時更改。不要依賴於錯誤消息本身。**|

範例錯誤:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

以下是特定於託管標識的典型服務結構錯誤的清單:

| 程式碼 | 訊息 | 描述 | 
| ----------- | ----- | ----------------- |
| 找不到秘密標題 | 在請求標頭中找不到機密。 | 請求未提供身份驗證代碼。 | 
| 託管身份找不到 | 找不到指定應用程式主機的託管標識。 | 應用程式沒有標識,或者身份驗證代碼未知。 |
| 參數無效 | 參數"資源"不應為空字串或空字串。 | 請求中未提供資源(訪問群體)。 |
| InvalidApiVersion | 不支援 api 版本 ''。 支援的版本是"2019-07-01 預覽」。。 | 缺少或不支援的請求 URI 中指定的 API 版本。 |
| InternalServerError | 發生錯誤。 | 託管標識子系統中遇到錯誤,可能是在 Service Fabric 堆疊之外。 最有可能的原因是為資源指定的錯誤值(檢查尾隨的"/"? | 

## <a name="retry-guidance"></a>重試指引 

通常,唯一可重試的錯誤代碼是 429(請求太多);內部伺服器錯誤/5xx 錯誤代碼可能是可重試的,但原因可能是永久性的。 

限制限制適用於對託管標識子系統進行的調用數 , 特別是「上游」依賴項(託管標識 Azure 服務或安全權杖服務)。 Service Fabric 在管道中的各個級別緩存令牌,但考慮到所涉及的元件的分散式性質,調用方可能會遇到不一致的限制回應(即,在應用程式的一個節點/實例上受到限制,但在請求相同標識的權杖時,不會在不同的節點上受到限制)。設置限制條件時,來自同一應用程式的後續請求可能會失敗,HTTP 狀態代碼 429(請求太多),直到條件被清除。  

建議使用指數回退重試由於限制而失敗的請求,如下所示: 

| 撥號索引 | 收到 429 時的操作 | 
| --- | --- | 
| 1 | 等待 1 秒並重試 |
| 2 | 等待 2 秒並重試 |
| 3 | 等待 4 秒並重試 |
| 4 | 等待 8 秒並重試 |
| 4 | 等待 8 秒並重試 |
| 5 | 等待 16 秒並重試 |

## <a name="resource-ids-for-azure-services"></a>Azure 服務的資源識別碼
有關[支援 Azure](../active-directory/managed-identities-azure-resources/services-support-msi.md) AD 的資源清單及其各自的資源標識,請參閱支援 Azure AD 身份驗證的 Azure 服務。

## <a name="next-steps"></a>後續步驟
* [使用系統配置的託管識別部署 Azure 服務結構應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用使用者配置的託管識別部署 Azure 服務結構應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [授予 Azure 服務結構應用程式對其他 Azure 資源的存取](./how-to-grant-access-other-resources.md)
