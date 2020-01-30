---
title: Azure API 管理原則運算式 | Microsoft Docs
description: 了解 Azure API 管理中的原則運算式。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: 6614e70d130abe46067c657bda3ccdd7000caddc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845285"
---
# <a name="api-management-policy-expressions"></a>API 管理原則運算式
本文討論7中的C#原則運算式語法。 每個運算式皆可存取以隱含方式提供的[內容](api-management-policy-expressions.md#ContextVariables)變數，以及允許的 .NET Framework 類型[子集](api-management-policy-expressions.md#CLRTypes)。

詳細資訊：

- 了解如何提供內容資訊給您的後端服務。 使用[設定查詢字串參數](api-management-transformation-policies.md#SetQueryStringParameter)和[設定 HTTP 標頭](api-management-transformation-policies.md#SetHTTPheader)原則來提供此資訊。
- 了解如何使用[驗證 JWT](api-management-access-restriction-policies.md#ValidateJWT) 原則來根據權杖宣告預先授與作業的存取權。
- 了解如何使用 [API 檢查器](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/)追蹤，以查看原則的評估方式和那些評估結果。
- 了解如何使用運算式搭配[從快取中取得](api-management-caching-policies.md#GetFromCache)和[儲存至快取](api-management-caching-policies.md#StoreToCache)原則，來設定 API 管理回應的快取。 設定一段持續時間，使其符合備用服務中 `Cache-Control` 指示詞所指定的後端服務回應快取。
- 了解如何執行內容篩選。 使用[控制流程](api-management-advanced-policies.md#choose)和[設定本文](api-management-transformation-policies.md#SetBody)原則，移除「從後端收到的回應」中的資料元素。
- 若要下載原則陳述式，請參閱 [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub 存放庫。


## <a name="Syntax"></a> 語法
單一陳述式的運算式會以 `@(expression)` 括住，其中 `expression` 是格式正確的 C# 運算式陳述式。

多重陳述式的運算式則會以 `@{expression}` 括住。 多重陳述式運算式內的所有程式碼路徑都必須以 `return` 陳述式結尾。

## <a name="PolicyExpressionsExamples"></a> 範例

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="PolicyExpressionsUsage"></a>使用量
如果原則參考未另行指定，則可以在任何 API 管理[原則](api-management-policies.md)中，使用運算式作為屬性值或文字值。

> [!IMPORTANT]
> 當您使用原則運算式時，若原則已定義，則只能對原則運算式進行有限的驗證。 執行階段上的閘道會執行運算式，原則運算式產生的任何例外狀況都會導致執行階段錯誤。

## <a name="CLRTypes"></a>原則運算式中允許的 .NET Framework 類型
下表列出原則運算式中允許的 .NET Framework 類型和其成員。

|類型|支援的成員|
|--------------|-----------------------|
|Newtonsoft。格式化|所有|
|Newtonsoft. Json. Jsonconvert.deserializeobject|SerializeObject、DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|所有|
|Newtonsoft.Json.Linq.JArray|所有|
|Newtonsoft.Json.Linq.JConstructor|所有|
|Newtonsoft.Json.Linq.JContainer|所有|
|Newtonsoft.Json.Linq.JObject|所有|
|Newtonsoft.Json.Linq.JProperty|所有|
|Newtonsoft.Json.Linq.JRaw|所有|
|Newtonsoft.Json.Linq.JToken|所有|
|Newtonsoft.Json.Linq.JTokenType|所有|
|Newtonsoft.Json.Linq.JValue|所有|
|System.Array|所有|
|System.bitconverter|所有|
|System.Boolean|所有|
|System.Byte|所有|
|System.Char|所有|
|< TKey，TValue > 的 Generic 字典|所有|
|HashSet\<T >|所有|
|\<T > 的 system.object|所有|
|< TKey，TValue > 的一般 IDictionary|所有|
|\<T > 的 system.object|所有|
|\<T > 的 system.object|所有|
|System.object\<T >|所有|
|Ireadonlycollection t>\<T >|所有|
|..Ireadonlydictionary<string < TKey，TValue >|所有|
|Iset<t>\<T >|所有|
|KeyValuePair < TKey，TValue >|所有|
|\<T > 的清單|所有|
|\<T > 的 system.object|所有|
|\<T > 的 system.object|所有|
|System.Convert|所有|
|System.DateTime|（函式）、Add、AddDays、Time.addhours、AddMilliseconds、AddMinutes、AddMonths、AddSeconds、AddTicks、AddYears、Date、Day、DayOfWeek、DayOfYear、DaysInMonth、Hour、IsDaylightSavingTime、IsLeapYear、int32.maxvalue、毫秒、Minute、MinValue、Month、Now、Parse、Second、減法、刻度、TimeOfDay、Today、ToString、UtcNow、Year|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|所有|
|System.Decimal|所有|
|System.Double|所有|
|Exception|所有|
|System.Guid|所有|
|System.Int16|所有|
|System.Int32|所有|
|System.Int64|所有|
|System.io.stringreader>|所有|
|StringWriter|所有|
|System.object|所有|
|System.Math|所有|
|System.MidpointRounding|所有|
|System.Net.WebUtility|所有|
|System。 Nullable|所有|
|System.Random|所有|
|System.SByte|所有|
|Asymmetricalgorithm 之。|所有|
|CipherMode。|所有|
|System.Security.Cryptography.HashAlgorithm|所有|
|HashAlgorithmName。|所有|
|System.Security.Cryptography.HMAC|所有|
|System.Security.Cryptography.HMACMD5|所有|
|System.Security.Cryptography.HMACSHA1|所有|
|System.Security.Cryptography.HMACSHA256|所有|
|HMACSHA384。|所有|
|HMACSHA512。|所有|
|System.Security.Cryptography.KeyedHashAlgorithm|所有|
|System.Security.Cryptography.MD5|所有|
|System.web. 安全密碼編譯. Oid|所有|
|PaddingMode。|所有|
|System.Security.Cryptography.RNGCryptoServiceProvider|所有|
|系統安全密碼編譯. RSA|所有|
|RSAEncryptionPadding。|所有|
|RSASignaturePadding。|所有|
|System.Security.Cryptography.SHA1|所有|
|System.Security.Cryptography.SHA1Managed|所有|
|System.Security.Cryptography.SHA256|所有|
|System.Security.Cryptography.SHA256Managed|所有|
|System.Security.Cryptography.SHA384|所有|
|System.Security.Cryptography.SHA384Managed|所有|
|System.Security.Cryptography.SHA512|所有|
|System.Security.Cryptography.SHA512Managed|所有|
|System.security.cryptography.symmetricalgorithm。|所有|
|System.security.cryptography.x509certificates.x509certificate2. PublicKey。|所有|
|System.security.cryptography.x509certificates.x509certificate2. RSACertificateExtensions。|所有|
|System.security.cryptography.x509certificates.x509certificate2. System.security.cryptography.x509certificates.x500distinguishedname。|名稱|
|System.security.cryptography.x509certificates.x509certificate2. X509Certificate。|所有|
|System.Security.Cryptography.X509Certificates.X509Certificate2|所有|
|System.security.cryptography.x509certificates.x509certificate2. X509ContentType。|所有|
|System.security.cryptography.x509certificates.x509certificate2. X509NameType。|所有|
|System.Single|所有|
|System.String|所有|
|StringComparer|所有|
|StringComparison|所有|
|System.StringSplitOptions|所有|
|System.Text.Encoding|所有|
|System.Text.RegularExpressions.Capture|索引、長度、值|
|System.Text.RegularExpressions.CaptureCollection|計數、項目|
|System.Text.RegularExpressions.Group|擷取、成功|
|System.Text.RegularExpressions.GroupCollection|計數、項目|
|System.Text.RegularExpressions.Match|空白、群組、結果|
|System.Text.RegularExpressions.Regex|（函式）、IsMatch、比對、Match、Replace、Unescape、Split|
|System.Text.RegularExpressions.RegexOptions|所有|
|System.object|所有|
|System.TimeSpan|所有|
|系統時區|所有|
|TimeZoneInfo. AdjustmentRule|所有|
|TimeZoneInfo. TransitionTime|所有|
|System.TimeZoneInfo|所有|
|System.Tuple|所有|
|System.UInt16|所有|
|System.UInt32|所有|
|System.UInt64|所有|
|System.Uri|所有|
|UriPartial|所有|
|System.Xml.Linq.Extensions|所有|
|System.Xml.Linq.XAttribute|所有|
|System.Xml.Linq.XCData|所有|
|System.Xml.Linq.XComment|所有|
|System.Xml.Linq.XContainer|所有|
|System.Xml.Linq.XDeclaration|所有|
|System.Xml.Linq.XDocument|全部，但不包括： Load|
|System.Xml.Linq.XDocumentType|所有|
|System.Xml.Linq.XElement|所有|
|System.Xml.Linq.XName|所有|
|System.Xml.Linq.XNamespace|所有|
|System.Xml.Linq.XNode|所有|
|System.Xml.Linq.XNodeDocumentOrderComparer|所有|
|System.Xml.Linq.XNodeEqualityComparer|所有|
|System.Xml.Linq.XObject|所有|
|System.Xml.Linq.XProcessingInstruction|所有|
|System.Xml.Linq.XText|所有|
|System.Xml.XmlNodeType|所有|

## <a name="ContextVariables"></a>內容變數
名為 `context` 的變數可在每個原則[運算式](api-management-policy-expressions.md#Syntax)中隱含地使用。 其成員會提供與 `\request` 相關的資訊。 所有 `context` 成員都是唯讀的。

|內容變數|允許的方法、屬性和參數值|
|----------------------|-------------------------------------------------------|
|內容|[Api](#ref-context-api)： [IApi](#ref-iapi)<br /><br /> [部署](#ref-context-deployment)<br /><br /> 經過時間：TimeSpan - Timestamp 值與目前時間之間的時間間隔<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [運算](#ref-context-operation)<br /><br /> [產品](#ref-context-product)<br /><br /> [要求](#ref-context-request)<br /><br /> RequestId：Guid - 唯一要求識別碼<br /><br /> [回應](#ref-context-response)<br /><br /> [訂用帳戶](#ref-context-subscription)<br /><br /> 時間戳記：DateTime - 收到要求的時間點<br /><br /> 追蹤：bool - 指出追蹤是開啟還是關閉 <br /><br /> [使用者](#ref-context-user)<br /><br /> [變數](#ref-context-variables)： ..ireadonlydictionary<string < 字串，物件 ><br /><br /> void Trace(訊息：字串)|
|<a id="ref-context-api"></a>內容.Api|識別碼︰字串<br /><br /> IsCurrentRevision: bool<br /><br />  名稱︰字串<br /><br /> 路徑︰字串<br /><br /> 修訂：字串<br /><br /> ServiceUrl： [iurl.query.getvalueordefault](#ref-iurl)<br /><br /> 版本：字串 |
|<a id="ref-context-deployment"></a>內容.部署|區域︰字串<br /><br /> ServiceName︰字串<br /><br /> 憑證：IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>內容.LastError|來源︰字串<br /><br /> 原因︰字串<br /><br /> 訊息︰字串<br /><br /> 範圍︰字串<br /><br /> 區段︰字串<br /><br /> 路徑︰字串<br /><br /> PolicyId︰字串<br /><br /> 如需 context.LastError 的詳細資訊，請參閱[錯誤處理](api-management-error-handling-policies.md)。|
|<a id="ref-context-operation"></a>內容.運營|識別碼︰字串<br /><br /> 方法︰字串<br /><br /> 名稱︰字串<br /><br /> UrlTemplate︰字串|
|<a id="ref-context-product"></a>內容.基礎|Api： IEnumerable <[IApi](#ref-iapi)\><br /><br /> ApprovalRequired：bool<br /><br /> 群組： IEnumerable <[IGroup](#ref-igroup)\><br /><br /> 識別碼︰字串<br /><br /> 名稱︰字串<br /><br /> 狀態︰列舉 ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit：int?<br /><br /> SubscriptionRequired：bool|
|<a id="ref-context-request"></a>內容.邀請|Body：如果要求沒有內文，則為[IMessageBody](#ref-imessagebody)或 `null`。<br /><br /> 憑證︰System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [標頭](#ref-context-request-headers)： ..ireadonlydictionary<string < 字串，string [] ><br /><br /> IpAddress︰字串<br /><br /> MatchedParameters︰IReadOnlyDictionary<string, string><br /><br /> 方法︰字串<br /><br /> OriginalUrl： [iurl.query.getvalueordefault](#ref-iurl)<br /><br /> Url： [iurl.query.getvalueordefault](#ref-iurl)|
|<a id="ref-context-request-headers"></a>字串內容。CoNtext.variables.getvalueordefault<t （headerName： string，defaultValue： string）|headerName︰字串<br /><br /> defaultValue︰字串<br /><br /> 如果找不到標頭，則傳回以逗號分隔的要求標頭值或 `defaultValue`。|
|<a id="ref-context-response"></a>內容.回應|主體： [IMessageBody](#ref-imessagebody)<br /><br /> [標頭](#ref-context-response-headers)： ..ireadonlydictionary<string < 字串，string [] ><br /><br /> StatusCode：int<br /><br /> StatusReason︰字串|
|<a id="ref-context-response-headers"></a>字串內容。CoNtext.variables.getvalueordefault<t （headerName： string，defaultValue： string）|headerName︰字串<br /><br /> defaultValue︰字串<br /><br /> 如果找不到標頭，則傳回以逗號分隔的回應標頭值或 `defaultValue`。|
|<a id="ref-context-subscription"></a>內容.預定|CreatedTime：DateTime<br /><br /> EndDate：DateTime?<br /><br /> 識別碼︰字串<br /><br /> 索引鍵︰字串<br /><br /> 名稱︰字串<br /><br /> PrimaryKey︰字串<br /><br /> SecondaryKey︰字串<br /><br /> StartDate：DateTime?|
|<a id="ref-context-user"></a>內容.使用者|電子郵件︰字串<br /><br /> FirstName︰字串<br /><br /> 群組： IEnumerable <[IGroup](#ref-igroup)\><br /><br /> 識別碼︰字串<br /><br /> 身分識別： IEnumerable <[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName︰字串<br /><br /> 附註︰字串<br /><br /> RegistrationDate︰DateTime|
|<a id="ref-iapi"></a>IApi|識別碼︰字串<br /><br /> 名稱︰字串<br /><br /> 路徑︰字串<br /><br /> 通訊協定︰IEnumerable<string\><br /><br /> ServiceUrl： [iurl.query.getvalueordefault](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames： [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|識別碼︰字串<br /><br /> 名稱︰字串|
|<a id="ref-imessagebody"></a>IMessageBody|As < T\>（preserveContent： bool = false）：其中 T： string，byte []，JObject，JToken，JArray，System.xml.linq.xnode>，System.xml.linq.xelement>，XDocument<br /><br /> `context.Request.Body.As<T>` 和 `context.Response.Body.As<T>` 方法是用來讀取指定類型 `T` 的要求和回應訊息主體。 根據預設，該方法會使用原始訊息本文資料流，並使它在傳回後無法使用。 若要避免那種情況，並讓方法在本文資料流的複本上進行操作，請將 `preserveContent` 參數設定為 `true`。 請移至[這裡](api-management-transformation-policies.md#SetBody)來查看範例。|
|<a id="ref-iurl"></a>Iurl.query.getvalueordefault|主機︰字串<br /><br /> 路徑︰字串<br /><br /> 連接埠︰int<br /><br /> [查詢](#ref-iurl-query)： ..ireadonlydictionary<string < 字串，string [] ><br /><br /> QueryString︰字串<br /><br /> 配置︰字串|
|<a id="ref-iuseridentity"></a>IUserIdentity|識別碼︰字串<br /><br /> 提供者︰字串|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|標頭︰字串<br /><br /> 查詢︰字串|
|<a id="ref-iurl-query"></a>字串 Iurl.query.getvalueordefault。 CoNtext.variables.getvalueordefault<t （queryParameterName： string，defaultValue： string）|queryParameterName︰字串<br /><br /> defaultValue︰字串<br /><br /> 如果找不到參數，則傳回以逗號分隔的查詢參數值或 `defaultValue`。|
|<a id="ref-context-variables"></a>T 內容。CoNtext.variables.getvalueordefault<t < T\>（variableName： string，defaultValue： T）|variableName︰字串<br /><br /> defaultValue︰T<br /><br /> 如果找不到變數，則傳回轉換為 `T` 或 `defaultValue` 的變數值。<br /><br /> 如果指定的類型不符合所傳回變數的實際類型，此方法便會擲回例外狀況。|
|BasicAuthCredentials AsBasic(輸入：此字串)|輸入︰字串<br /><br /> 如果輸入參數包含有效的 HTTP 基本驗證授權要求標頭值，則方法會傳回類型為 `BasicAuthCredentials` 的物件；否則方法會傳回 null。|
|bool TryParseBasic(輸入：此字串、結果：out BasicAuthCredentials)|輸入︰字串<br /><br /> 結果︰out BasicAuthCredentials<br /><br /> 如果輸入參數包含要求標頭中的有效 HTTP 基本驗證授權值，則方法會傳回 `true`，且結果參數包含類型為 `BasicAuthCredentials` 的值；否則方法會傳回 `false`。|
|BasicAuthCredentials|密碼︰字串<br /><br /> UserId︰字串|
|Jwt AsJwt(輸入：此字串)|輸入︰字串<br /><br /> 如果輸入參數包含有效的 JWT 權杖值，則方法會傳回類型為 `Jwt` 的物件；否則方法會傳回 `null`。|
|bool TryParseJwt(輸入：此字串、結果：out Jwt)|輸入︰字串<br /><br /> 結果：out Jwt<br /><br /> 如果輸入參數包含有效的 JWT 權杖值，則方法會傳回 `true`，且結果參數包含類型為 `Jwt` 的值；否則方法會傳回 `false`。|
|Jwt|演算法︰字串<br /><br /> 對象︰IEnumerable<string\><br /><br /> 宣告︰IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime：DateTime?<br /><br /> 識別碼︰字串<br /><br /> 簽發者︰字串<br /><br /> IssuedAt： DateTime？<br /><br /> NotBefore︰DateTime?<br /><br /> 主旨︰字串<br /><br /> 類型：字串|
|string Jwt.Claims.GetValueOrDefault(claimName：字串、defaultValue：字串)|claimName︰字串<br /><br /> defaultValue︰字串<br /><br /> 如果找不到標頭，則傳回以逗號分隔的宣告值或 `defaultValue`。|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|輸入 - 要加密的純文字<br /><br />alg - 對稱加密演算法的名稱<br /><br />金鑰 - 加密金鑰<br /><br />iv - 初始化向量<br /><br />傳回加密的純文字。|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|輸入 - 要加密的純文字<br /><br />alg - 加密演算法<br /><br />傳回加密的純文字。|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|輸入 - 要加密的純文字<br /><br />alg - 加密演算法<br /><br />金鑰 - 加密金鑰<br /><br />iv - 初始化向量<br /><br />傳回加密的純文字。|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|輸入 - 要解密的 Cypher 文字<br /><br />alg - 對稱加密演算法的名稱<br /><br />金鑰 - 加密金鑰<br /><br />iv - 初始化向量<br /><br />傳回純文字。|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|輸入 - 要解密的 Cypher 文字<br /><br />alg - 加密演算法<br /><br />傳回純文字。|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|輸入 - 要解密的 Cypher 文字<br /><br />alg - 加密演算法<br /><br />金鑰 - 加密金鑰<br /><br />iv - 初始化向量<br /><br />傳回純文字。|
|bool VerifyNoRevocation （輸入：此 System.security.cryptography.x509certificates.x509certificate2. X509Certificate2）|執行 x.509 鏈驗證，而不檢查憑證撤銷狀態。<br /><br />輸入憑證物件<br /><br />如果驗證成功，則傳回 `true`;如果驗證失敗，則 `false`。|


## <a name="next-steps"></a>後續步驟

如需使用原則的詳細資訊，請參閱︰

+ [API 管理中的原則](api-management-howto-policies.md)
+ [轉換 API](transform-api.md)
+ [原則參考文件](api-management-policy-reference.md)，取得原則陳述式及其設定的完整清單
+ [原則範例](policy-samples.md)
