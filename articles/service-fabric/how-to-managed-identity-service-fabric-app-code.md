---
title: 搭配應用程式使用受控識別
description: 如何在 Azure 中使用受控識別 Service Fabric 應用程式代碼來存取 Azure 服務。
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: 07f960c01367ab42a434a8c2e1e276d9c5f7bd11
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86253638"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>如何利用 Service Fabric 應用程式的受控識別來存取 Azure 服務

Service Fabric 應用程式可以利用受控識別來存取支援 Azure Active Directory 型驗證的其他 Azure 資源。 應用程式可以取得代表其身分識別的[存取權杖](../active-directory/develop/developer-glossary.md#access-token)，這可能是系統指派或使用者指派，並使用它做為「持有人」權杖，向另一個服務（也稱為[受保護的資源伺服器](../active-directory/develop/developer-glossary.md#resource-server)）驗證其本身。 權杖代表指派給 Service Fabric 應用程式的身分識別，而且只會發行至 Azure 資源， (包括共用該身分識別的 SF 應用程式) 。 如需受控識別的詳細描述，以及系統指派和使用者指派的識別之間的差異，請參閱[受控識別總覽](../active-directory/managed-identities-azure-resources/overview.md)檔。 在本文中，我們會將受管理身分識別功能的 Service Fabric 應用程式稱為[用戶端應用](../active-directory/develop/developer-glossary.md#client-application)程式。

> [!IMPORTANT]
> 受控識別代表與包含資源之訂用帳戶相關聯之對應 Azure AD 租使用者中的 Azure 資源與服務主體之間的關聯。 因此，在 Service Fabric 的內容中，受管理的身分識別僅支援部署為 Azure 資源的應用程式。 

> [!IMPORTANT]
> 在使用 Service Fabric 應用程式的受控識別之前，必須先將受保護資源的存取權授與用戶端應用程式。 請參閱[支援 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)清單，以檢查支援，然後前往個別服務的檔，以瞭解如何將身分識別存取權授與所需資源的特定步驟。 

## <a name="acquiring-an-access-token-using-rest-api"></a>使用 REST API 取得存取權杖
在啟用受控識別的叢集中，Service Fabric 執行時間會公開可供應用程式用來取得存取權杖的 localhost 端點。 端點可在叢集的每個節點上使用，而且可供該節點上的所有實體存取。 授權的呼叫者可以藉由呼叫此端點並出示驗證碼，來取得存取權杖。程式碼是由每個不同服務程式代碼套件啟用的 Service Fabric 執行時間所產生，而且會系結至裝載該服務程式代碼套件的進程存留期。

具體而言，受管理身分識別功能的 Service Fabric 服務的環境將會使用下列變數植入：
- ' IDENTITY_ENDPOINT '：對應至服務受控識別的 localhost 端點
- ' IDENTITY_HEADER '：代表目前節點上服務的唯一驗證碼
- ' IDENTITY_SERVER_THUMBPRINT '： service fabric 受管理身分識別伺服器的指紋

> [!IMPORTANT]
> 應用程式程式碼應該將 ' IDENTITY_HEADER ' 環境變數的值視為機密資料-它不應該記錄或簡易性。 驗證碼沒有本機節點以外的值，或裝載服務的進程終止之後，但它確實代表 Service Fabric 服務的身分識別，因此應該使用與存取權杖本身相同的預防措施來處理。

若要取得權杖，用戶端會執行下列步驟：
- 藉由將受控識別端點 (IDENTITY_ENDPOINT 值) 與 API 版本以及) 權杖所需的資源 (物件進行串連，形成 URI
- 為指定的 URI 建立 GET HTTP (s) 要求
- 新增適當的伺服器憑證驗證邏輯
- 新增驗證程式代碼 (IDENTITY_HEADER 值) 做為要求的標頭
- 提交要求

成功的回應會包含代表所產生之存取權杖的 JSON 承載，以及描述它的中繼資料。 失敗的回應也會包含失敗的說明。 如需錯誤處理的其他詳細資料，請參閱下文。

存取權杖將會由 Service Fabric (節點、叢集、資源提供者服務) 的各種層級進行快取，因此成功的回應不一定表示已直接發出權杖以回應使用者應用程式的要求。 權杖將會快取到低於其存留期，因此應用程式保證會收到有效的權杖。 建議應用程式程式碼快取它所取得的任何存取權杖;快取索引鍵應該包含 () 物件的衍生。 

範例要求：
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
其中：

| 元素 | 描述 |
| ------- | ----------- |
| `GET` | HTTP 指令動詞，指出您想要擷取端點中的資料。 在此案例中是 OAuth 存取權杖。 | 
| `https://localhost:2377/metadata/identity/oauth2/token` | 透過 IDENTITY_ENDPOINT 環境變數提供 Service Fabric 應用程式的受控識別端點。 |
| `api-version` | 查詢字串參數，指定受控識別權杖服務的 API 版本;目前唯一接受的值是 `2019-07-01-preview` ，而且可能會變更。 |
| `resource` | 查詢字串參數，指出目標資源的應用程式識別碼 URI。 這會反映為 `aud` (物件) 已發行權杖的宣告。 此範例會要求權杖來存取 Azure Key Vault，其應用程式識別碼 URI 為 HTTPs： \/ /vault.azure.net/。 |
| `Secret` | Service Fabric 服務的 Service Fabric 受控識別權杖服務所需的 HTTP 要求標頭欄位，用來驗證呼叫者。 此值是由 SF 執行時間透過 IDENTITY_HEADER 環境變數提供。 |


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

| 元素 | 描述 |
| ------- | ----------- |
| `token_type` | Token 的類型;在此情況下，就是「持有人」存取權杖，這表示 ( 「持有人」此權杖的 ) 是權杖的預定主旨。 |
| `access_token` | 所要求的存取權杖。 呼叫受保護的 REST API 時，權杖會內嵌在 `Authorization` 要求標頭欄位中成為「持有人」權杖，以允許 API 驗證呼叫端。 | 
| `expires_on` | 存取權杖的到期時間戳記;以 "1970-01-01T0：0： 0Z UTC" 的秒數表示，並對應至權杖的宣告 `exp` 。 在此情況下，此權杖會在 2019-08-08T06：10： 11 + 00： 00 (在 RFC 3339 中到期) |
| `resource` | 發出存取權杖的資源（透過 `resource` 要求的查詢字串參數指定）; 對應至權杖的 ' aud ' 宣告。 |


## <a name="acquiring-an-access-token-using-c"></a>使用 C 取得存取權杖#
上述會變成 c # 中的：

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
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>使用受控識別從 Service Fabric 應用程式存取 Key Vault
這個範例會以上述方式建立，以示範如何使用受控識別存取儲存在 Key Vault 中的秘密。

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
HTTP 回應標頭的 [狀態碼] 欄位會指出要求的成功狀態。「200確定」狀態表示成功，回應將包含上述的存取權杖。 以下是可能錯誤回應的簡短列舉。

| 狀態碼 | 錯誤原因 | 處理方式 |
| ----------- | ------------ | ------------- |
| 404 找不到。 | 不明的驗證碼，或未將受控識別指派給應用程式。 | 更正應用程式安裝或權杖取得代碼。 |
| 429 要求太多。 |  已達到節流限制，由 AAD 或 SF 強加。 | 使用指數輪詢重試。 請參閱下面的指引。 |
| 要求中的 4xx 錯誤。 | 一個或多個要求參數不正確。 | 請勿重試。  檢查錯誤詳細資料以取得更多資訊。  4xx 錯誤是設計階段錯誤。|
| 來自服務的5xx 錯誤。 | 受控識別子系統或 Azure Active Directory 傳回暫時性錯誤。 | 很容易就能在短時間後重試。 在重試時，您可能會遇到 (429) 的節流狀況。|

如果發生錯誤，對應的 HTTP 回應主體會包含 JSON 物件，其中含有錯誤詳細資料：

| 元素 | 說明 |
| ------- | ----------- |
| code | 錯誤碼。 |
| correlationId | 可用於進行偵錯工具的相互關聯識別碼。 |
| message | 錯誤的詳細資訊描述。 **錯誤描述可以隨時變更。不依賴錯誤訊息本身。**|

範例錯誤：
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

以下是受控識別特有的一般 Service Fabric 錯誤清單：

| 程式碼 | 訊息 | 描述 | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | 在要求標頭中找不到秘密。 | 要求未提供驗證碼。 | 
| ManagedIdentityNotFound | 找不到指定之應用程式主機的受控識別。 | 應用程式沒有身分識別，或驗證碼不明。 |
| ArgumentNullOrEmpty | 參數 ' resource ' 不應為 null 或空字串。 | 要求中未提供資源 (物件) 。 |
| InvalidApiVersion | 不支援 api 版本 ' '。 支援的版本為 ' 2019-07-01-preview '。 | 要求 URI 中指定的 API 版本遺失或不受支援。 |
| InternalServerError | 發生錯誤。 | 受控識別子系統中發生錯誤，可能是在 Service Fabric 堆疊外部。 最可能的原因是為資源指定的值不正確 (檢查尾端的 '/'？ )  | 

## <a name="retry-guidance"></a>重試指引 

通常唯一可重試的錯誤碼為 429 (太多要求) ;內部伺服器錯誤/5xx 錯誤碼可能是可重試的，但原因可能是永久性的。 

節流限制適用于對受控識別子系統發出的呼叫數，特別是「上游」相依性 (受控識別 Azure 服務或安全權杖服務) 。 Service Fabric 會在管線中的不同層級快取權杖，但基於相關元件的分散式本質，呼叫者可能會遇到不一致的節流回應 (也就是在應用程式的一個節點/實例上進行節流，但在要求相同身分識別的權杖時，不是在不同的節點上。 ) 設定節流條件時，來自相同應用程式的後續要求可能會失敗，並出現 HTTP 狀態碼 429 (太多要求，直到清除該條件為止。)   

建議因為節流會以指數輪詢重試而導致要求失敗，如下所示： 

| 呼叫索引 | 接收429的動作 | 
| --- | --- | 
| 1 | 等候1秒後重試 |
| 2 | 等待2秒後重試 |
| 3 | 等待4秒後重試 |
| 4 | 等待8秒後重試 |
| 4 | 等待8秒後重試 |
| 5 | 等候16秒後重試 |

## <a name="resource-ids-for-azure-services"></a>Azure 服務的資源識別碼
如需支援 Azure AD 的資源清單及其各自的資源識別碼，請參閱[支援 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)。

## <a name="next-steps"></a>後續步驟
* [使用系統指派的受控識別來部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用使用者指派的受控識別來部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [將其他 Azure 資源的存取權授與 Azure Service Fabric 應用程式](./how-to-grant-access-other-resources.md)
