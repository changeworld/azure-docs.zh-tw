---
title: Azure API 管理存取限制原則 | Microsoft Docs
description: 了解可用於 Azure API 管理中的存取限制原則。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 05ca16ad828525f2b09e4adf8f75be4667254614
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535180"
---
# <a name="api-management-access-restriction-policies"></a>API 管理存取限制原則

本主題提供下列 API 管理原則的參考。 如需有關新增和設定原則的資訊，請參閱 [API 管理中的原則](https://go.microsoft.com/fwlink/?LinkID=398186)。

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a> 存取限制原則

-   [檢查 HTTP 標頭](api-management-access-restriction-policies.md#CheckHTTPHeader) - 強制必須存在和/或強制採用 HTTP 標頭的值。
-   [依訂閱限制呼叫率](api-management-access-restriction-policies.md#LimitCallRate) - 以訂閱為單位，限制呼叫率以避免 API 使用量暴增。
-   [依金鑰限制呼叫率](#LimitCallRateByKey) - 以金鑰為單位，限制呼叫率以避免 API 使用量暴增。
-   [限制呼叫端 IP](api-management-access-restriction-policies.md#RestrictCallerIPs) - 篩選 (允許/拒絕) 來自特定 IP 位址和/或位址範圍的呼叫。
-   [依訂閱設定使用量配額](api-management-access-restriction-policies.md#SetUsageQuota) - 以訂閱為單位，讓您可以強制採用可續訂或有存留期呼叫量與 (或) 頻寬配額。
-   [依金鑰設定使用量配額](#SetUsageQuotaByKey) - 以金鑰為單位，讓您可以強制採用可續訂或有存留期呼叫量與 (或) 頻寬配額。
-   [驗證 JWT](api-management-access-restriction-policies.md#ValidateJWT) - 強制擷取自指定 HTTP 標頭或指定查詢參數的 JWT 必須存在且有效。

> [!TIP]
> 您可以針對不同的用途，在不同的範圍中使用存取限制原則。 例如，您可以在 API 層級套用原則，以透過 AAD 驗證來保護整個 API，也可以 `validate-jwt` 將它套用在 api 作業層級上， `claims` 以進行更細微的控制。

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a>檢查 HTTP 標頭

使用 `check-header` 原則來強制各要求均需具有指定的 HTTP 標頭。 您可以選擇性地查看標頭是否具有特定值，或檢查允許的值範圍。 如果檢查失敗，原則就會終止要求處理，並傳回原則所指定的 HTTP 狀態碼和錯誤訊息。

### <a name="policy-statement"></a>原則陳述式

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>範例

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>項目

| 名稱         | 描述                                                                                                                                   | 必要 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | 根元素。                                                                                                                                 | 是      |
| value        | 允許的 HTTP 標頭值。 指定多個值元素時，如果其中任何一個值相符，則會將檢查視為成功。 | 否       |

### <a name="attributes"></a>屬性

| 名稱                       | 描述                                                                                                                                                            | 必要 | 預設 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | 如果標頭不存在或具有無效值，要在 HTTP 回應本文中傳回的錯誤訊息。 此訊息必須正確逸出任何特殊字元。 | 是      | N/A     |
| failed-check-httpcode      | 標頭不存在或具有無效值時所要傳回的 HTTP 狀態碼。                                                                                        | 是      | N/A     |
| header-name                | 要檢查的 HTTP 標頭名稱。                                                                                                                                  | 是      | N/A     |
| ignore-case                | 可以設定為 True 或 False。 如果設定為 True，則會在標頭值與一組可接受的值進行比較時，忽略大小寫。                                    | 是      | N/A     |

### <a name="usage"></a>使用量

此原則可用於下列原則[區段](./api-management-howto-policies.md#sections)和[範圍](./api-management-howto-policies.md#scopes)。

-   **原則區段︰** 輸入、輸出

-   **原則範圍：** 所有範圍

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a>依訂用帳戶限制呼叫頻率

`rate-limit` 原則藉由將指定時間週期內的呼叫頻率限制為指定次數，以防止每個訂用帳戶的 API 使用量暴增。 觸發此原則時，呼叫者會收到 `429 Too Many Requests` 回應狀態碼。

> [!IMPORTANT]
> 每份原則文件只能使用此原則一次。
>
> [原則運算式](api-management-policy-expressions.md)不能用於此原則的任何原則屬性中。

> [!CAUTION]
> 由於節流架構的分散本質，速率限制永遠不會完全正確。 已設定和實際允許的要求數目差異取決於要求數量和速率、後端延遲和其他因素。

> [!NOTE]
> 若要瞭解速率限制與配額之間的差異， [請參閱速率限制和配額。](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>原則陳述式

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### <a name="example"></a>範例

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>項目

| 名稱       | 描述                                                                                                                                                                                                                                                                                              | 必要 |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| rate-limit | 根元素。                                                                                                                                                                                                                                                                                            | 是      |
| api        | 新增一或多個這些元素，以對產品內的 Api 強加呼叫頻率限制。 產品和 API 呼叫頻率限制會獨立套用。 API 可以透過 `name` 或 `id` 參考。 如果同時提供兩個屬性，則會使用 `id` 而忽略 `name`。                    | 否       |
| 作業  | 新增一或多個這些元素，以對 API 內的作業強加呼叫頻率限制。 產品、API 和作業呼叫頻率限制會獨立套用。 作業可以透過 `name` 或 `id` 參考。 如果同時提供兩個屬性，則會使用 `id` 而忽略 `name`。 | 否       |

### <a name="attributes"></a>屬性

| 名稱           | 描述                                                                                           | 必要 | 預設 |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| NAME           | 要套用速率限制的 API 名稱。                                                | 是      | N/A     |
| calls          | 在 `renewal-period` 中指定的時間週期內允許的呼叫總數上限。 | 是      | N/A     |
| renewal-period | 重設配額的時間週期 (以秒為單位)。                                              | 是      | N/A     |

### <a name="usage"></a>使用量

此原則可用於下列原則[區段](./api-management-howto-policies.md#sections)和[範圍](./api-management-howto-policies.md#scopes)。

-   **原則區段︰** inbound

-   **原則範圍：** 產品、api、作業

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a>依金鑰限制呼叫頻率

> [!IMPORTANT]
> 這項功能無法在 API 管理的**使用**層中使用。

`rate-limit-by-key` 原則藉由將指定時間週期內的呼叫頻率限制為指定次數，以防止每個金鑰的 API 使用量暴增。 金鑰可以具有任意字串值，而且通常會使用原則運算式來提供。 可以新增選擇性增量條件，以指定哪些要求應該計入限制。 觸發此原則時，呼叫者會收到 `429 Too Many Requests` 回應狀態碼。

如需此原則範例的詳細資訊，請參閱[以 Azure API 管理進行進階要求節流](./api-management-sample-flexible-throttling.md)。

> [!CAUTION]
> 由於節流架構的分散本質，速率限制永遠不會完全正確。 已設定和實際允許的要求數目差異取決於要求數量和速率、後端延遲和其他因素。

> [!NOTE]
> 若要瞭解速率限制與配額之間的差異， [請參閱速率限制和配額。](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>原則陳述式

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>範例

在下列範例中，頻率限制是依呼叫端 IP 位址鍵入。

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>項目

| 名稱              | 描述   | 必要 |
| ----------------- | ------------- | -------- |
| 速率-依金鑰限制 | 根元素。 | 是      |

### <a name="attributes"></a>屬性

| 名稱                | 描述                                                                                           | 必要 | 預設 |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| calls               | 在 `renewal-period` 中指定的時間週期內允許的呼叫總數上限。 | 是      | N/A     |
| counter-key         | 用於頻率限制原則的金鑰。                                                             | 是      | N/A     |
| increment-condition | 此布林運算式指定要求是否應該計入配額 (`true`)。        | 否       | N/A     |
| renewal-period      | 重設配額的時間週期 (以秒為單位)。                                              | 是      | N/A     |

### <a name="usage"></a>使用量

此原則可用於下列原則[區段](./api-management-howto-policies.md#sections)和[範圍](./api-management-howto-policies.md#scopes)。

-   **原則區段︰** inbound

-   **原則範圍：** 所有範圍

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a>限制呼叫端 IP

`ip-filter` 原則可篩選 (允許/拒絕) 來自特定 IP 位址及/或位址範圍的呼叫。

### <a name="policy-statement"></a>原則陳述式

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>範例

在下列範例中，原則只允許來自指定的單一 IP 位址或 IP 位址範圍的要求

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>項目

| 名稱                                      | 描述                                         | 必要                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | 根元素。                                       | 是                                                            |
| address                                   | 指定要篩選的單一 IP 位址。   | 至少需要一個 `address` 或 `address-range` 元素。 |
| address-range from="位址" to="位址" | 指定要篩選的 IP 位址範圍。 | 至少需要一個 `address` 或 `address-range` 元素。 |

### <a name="attributes"></a>屬性

| 名稱                                      | 描述                                                                                 | 必要                                           | 預設 |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| address-range from="位址" to="位址" | 允許或拒絕存取的 IP 位址範圍。                                        | 使用 `address-range` 元素時必要。 | N/A     |
| ip-filter action="allow &#124; forbid"    | 指定允許或不允許指定的 IP 位址和範圍進行呼叫。 | 是                                                | N/A     |

### <a name="usage"></a>使用量

此原則可用於下列原則[區段](./api-management-howto-policies.md#sections)和[範圍](./api-management-howto-policies.md#scopes)。

-   **原則區段︰** inbound
-   **原則範圍：** 所有範圍

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a> 依訂用帳戶設定使用量配額

`quota` 原則會以訂用帳戶為單位，強制採用可續訂或有存留期呼叫量與 (或) 頻寬配額。

> [!IMPORTANT]
> 每份原則文件只能使用此原則一次。
>
> [原則運算式](api-management-policy-expressions.md)不能用於此原則的任何原則屬性中。

> [!NOTE]
> 若要瞭解速率限制與配額之間的差異， [請參閱速率限制和配額。](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>原則陳述式

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>範例

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>項目

| 名稱      | 描述                                                                                                                                                                                                                                                                                  | 必要 |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | 根元素。                                                                                                                                                                                                                                                                                | 是      |
| api       | 新增一或多個這些元素，以對產品內的 Api 強加呼叫配額。 產品和 API 呼叫配額會獨立套用。 API 可以透過 `name` 或 `id` 參考。 如果同時提供兩個屬性，則會使用 `id` 而忽略 `name`。                    | 否       |
| 作業 | 新增一或多個這些元素，以對 API 內的作業強加呼叫配額。 產品、API 和作業呼叫配額會獨立套用。 作業可以透過 `name` 或 `id` 參考。 如果同時提供兩個屬性，則會使用 `id` 而忽略 `name`。 | 否       |

### <a name="attributes"></a>屬性

| 名稱           | 描述                                                                                               | 必要                                                         | 預設 |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| NAME           | 套用配額的 API 或作業名稱。                                             | 是                                                              | N/A     |
| bandwidth      | 在 `renewal-period` 中指定的時間週期內允許的 KB 總數上限。 | 必須指定 `calls`、`bandwidth`，或同時指定兩者。 | N/A     |
| calls          | 在 `renewal-period` 中指定的時間週期內允許的呼叫總數上限。     | 必須指定 `calls`、`bandwidth`，或同時指定兩者。 | N/A     |
| renewal-period | 重設配額的時間週期 (以秒為單位)。                                                  | 是                                                              | N/A     |

### <a name="usage"></a>使用量

此原則可用於下列原則[區段](./api-management-howto-policies.md#sections)和[範圍](./api-management-howto-policies.md#scopes)。

-   **原則區段︰** inbound
-   **原則範圍︰** 產品

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a>依金鑰設定使用量配額

> [!IMPORTANT]
> 這項功能無法在 API 管理的**使用**層中使用。

`quota-by-key` 原則會以金鑰為單位，強制採用可續訂或有存留期呼叫量與 (或) 頻寬配額。 金鑰可以具有任意字串值，而且通常會使用原則運算式來提供。 可以新增選擇性增量條件，以指定哪些要求應該計入配額。 如果多個原則會使相同的金鑰值遞增，該值址會針對每個要求遞增一次。 達到呼叫限制時，呼叫端會收到 `403 Forbidden` 回應狀態碼。

如需此原則範例的詳細資訊，請參閱[以 Azure API 管理進行進階要求節流](./api-management-sample-flexible-throttling.md)。

> [!NOTE]
> 若要瞭解速率限制與配額之間的差異， [請參閱速率限制和配額。](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)

### <a name="policy-statement"></a>原則陳述式

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>範例

在下列範例中，配額是依呼叫端 IP 位址鍵入。

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>項目

| 名稱  | 描述   | 必要 |
| ----- | ------------- | -------- |
| quota | 根元素。 | 是      |

### <a name="attributes"></a>屬性

| 名稱                | 描述                                                                                               | 必要                                                         | 預設 |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| bandwidth           | 在 `renewal-period` 中指定的時間週期內允許的 KB 總數上限。 | 必須指定 `calls`、`bandwidth`，或同時指定兩者。 | N/A     |
| calls               | 在 `renewal-period` 中指定的時間週期內允許的呼叫總數上限。     | 必須指定 `calls`、`bandwidth`，或同時指定兩者。 | N/A     |
| counter-key         | 用於配額原則的金鑰。                                                                      | 是                                                              | N/A     |
| increment-condition | 此布林運算式指定要求是否應該計入配額 (`true`)。             | 否                                                               | N/A     |
| renewal-period      | 重設配額的時間週期 (以秒為單位)。                                                  | 是                                                              | N/A     |

### <a name="usage"></a>使用量

此原則可用於下列原則[區段](./api-management-howto-policies.md#sections)和[範圍](./api-management-howto-policies.md#scopes)。

-   **原則區段︰** inbound
-   **原則範圍：** 所有範圍

## <a name="validate-jwt"></a><a name="ValidateJWT"></a>驗證 JWT

`validate-jwt` 原則會強制擷取自指定 HTTP 標頭或指定查詢參數的 JWT 必須存在且有效。

> [!IMPORTANT]
> `validate-jwt` 原則會要求 `exp` 註冊的宣告包含在 JWT 權杖中 (除非已指定 `require-expiration-time` 屬性並設定為 `false`)。
> `validate-jwt` 原則支援 HS256 和 RS256 簽署演算法。 若為 HS256，必須以 base64 編碼形式內嵌於原則內的方式提供金鑰。 若為 RS256，必須透過 Open ID 設定端點提供金鑰。
> `validate-jwt` 原則支援使用下列加密演算法以對稱金鑰加密的權杖：A128CBC-HS256、A192CBC-HS384、A256CBC-HS512。

### <a name="policy-statement"></a>原則陳述式

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
</validate-jwt>

```

### <a name="examples"></a>範例

#### <a name="simple-token-validation"></a>簡單權杖驗證

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory 權杖驗證

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C 權杖驗證

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>根據權杖宣告授與作業的存取權

此範例示範如何使用「 [驗證 JWT](api-management-access-restriction-policies.md#ValidateJWT) 」原則，根據權杖宣告值來授權作業的存取權。

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>元素

| 元素             | 描述                                                                                                                                                                                                                                                                                                                                           | 必要 |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | 根元素。                                                                                                                                                                                                                                                                                                                                         | 是      |
| audiences           | 包含可呈現在權杖上之可接受的受眾宣告清單。 如果存在多個受眾值，則會嘗試每個值，直到全部試完 (即表示驗證失敗) 或其中一個值成功為止。 必須指定至少一個受眾。                                                                     | 否       |
| issuer-signing-keys | 用來驗證已簽署權杖的 Base64 編碼安全性金鑰清單。 如果存在多個安全性金鑰，則會嘗試每個金鑰，直到全部試完 (即表示驗證失敗) 或其中一個金鑰成功 (很適合用於權杖變換) 為止。 金鑰元素具有用來與 `kid` 宣告進行比對的選擇性 `id` 屬性。               | 否       |
| decryption-keys     | 用來將權杖解密的 Base64 編碼金鑰清單。 如果有多個安全性金鑰存在，則系統會嘗試每個金鑰，直到試完所有金鑰 (即表示驗證失敗) 或某個金鑰成功為止。 金鑰元素具有用來與 `kid` 宣告進行比對的選擇性 `id` 屬性。                                                 | 否       |
| issuers             | 可接受之簽發權杖的主體清單。 如果存在多個簽發者值，則會嘗試每個值，直到全部試完 (即表示驗證失敗) 或其中一個值成功為止。                                                                                                                                         | 否       |
| openid-config       | 此元素用於指定可從中取得簽署金鑰和簽發者之符合規範的 Open ID 設定端點。                                                                                                                                                                                                                        | 否       |
| required-claims     | 包含應存在於權杖上才會被視為有效的宣告清單。 當 `match` 屬性設定為 `all` 時，原則中的每個宣告值都必須存在於權杖，才能驗證成功。 當 `match` 屬性設定為 `any` 時，至少一個宣告必須存在於權杖，才能驗證成功。 | 否       |

### <a name="attributes"></a>屬性

| 名稱                            | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                            | 必要                                                                         | 預設                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| clock-skew                      | 時間範圍。 用來指定權杖簽發者和 API 管理執行個體的系統時鐘之間最大預期時間差異。                                                                                                                                                                                                                                                                                                               | 否                                                                               | 0 秒                                                                         |
| failed-validation-error-message | 如果 JWT 未通過驗證，在 HTTP 回應主體中傳回的錯誤訊息。 此訊息必須正確逸出任何特殊字元。                                                                                                                                                                                                                                                                                                 | 否                                                                               | 預設錯誤訊息視驗證問題而定，例如「JWT 不存在」。 |
| failed-validation-httpcode      | JWT 未通過驗證時所要傳回的 HTTP 狀態碼。                                                                                                                                                                                                                                                                                                                                                                                         | 否                                                                               | 401                                                                               |
| header-name                     | 保留權杖的 HTTP 標頭名稱。                                                                                                                                                                                                                                                                                                                                                                                                         | `header-name` `query-parameter-name` 或 `token-value` 必須指定其中一個。 | N/A                                                                               |
| query-parameter-name            | 保留權杖的查詢參數名稱。                                                                                                                                                                                                                                                                                                                                                                                                     | `header-name` `query-parameter-name` 或 `token-value` 必須指定其中一個。 | N/A                                                                               |
| token-值                     | 傳回包含 JWT 權杖之字串的運算式                                                                                                                                                                                                                                                                                                                                                                                                     | `header-name` `query-parameter-name` 或 `token-value` 必須指定其中一個。 | N/A                                                                               |
| id                              | `key` 元素的 `id` 屬性可讓您指定要與權杖中的 `kid` 宣告 (如果存在) 進行比對的字串，以找出適合用於簽章驗證的金鑰。                                                                                                                                                                                                                                           | 否                                                                               | N/A                                                                               |
| match                           | `claim` 元素的 `match` 屬性可指定原則中的每個宣告值是否都必須存在於權杖，才能驗證成功。 可能的值包括：<br /><br /> - `all` - 原則中的每個宣告值都必須存在於權杖，才能驗證成功。<br /><br /> - `any` - 至少一個宣告必須存在於權杖，才能驗證成功。                                                       | 否                                                                               | all                                                                               |
| require-expiration-time         | 布林值。 指定權杖中是否需有逾期宣告。                                                                                                                                                                                                                                                                                                                                                                               | 否                                                                               | true                                                                              |
| require-scheme                  | 權杖配置的名稱，例如「持有人」。 當已設定此屬性時，原則將會確定指定的結構描述存在於授權標頭值中。                                                                                                                                                                                                                                                                                    | 否                                                                               | N/A                                                                               |
| require-signed-tokens           | 布林值。 指定是否需要簽署權杖。                                                                                                                                                                                                                                                                                                                                                                                           | 否                                                                               | true                                                                              |
| separator                       | 字串。 指定用於從多重值宣告中擷取一組值的分隔符號 (例如 ",")。                                                                                                                                                                                                                                                                                                                                          | 否                                                                               | N/A                                                                               |
| url                             | 可從中取得 Open ID 設定中繼資料的 Open ID 設定端點 URL。 回應應該根據 URL 所定義的規格：`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`。 對於 Azure Active Directory，使用下列 URL：`https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` 代替您的目錄租用戶名稱，例如 `contoso.onmicrosoft.com`。 | 是                                                                              | N/A                                                                               |
| 輸出-token-variable 名稱      | 字串。 當 [`Jwt`](api-management-policy-expressions.md) 權杖驗證成功時，將會以類型物件的形式接收權杖值的內容變數名稱                                                                                                                                                                                                                                                                                     | 否                                                                               | N/A                                                                               |

### <a name="usage"></a>使用量

此原則可用於下列原則[區段](./api-management-howto-policies.md#sections)和[範圍](./api-management-howto-policies.md#scopes)。

-   **原則區段︰** inbound
-   **原則範圍：** 所有範圍

## <a name="next-steps"></a>後續步驟

如需使用原則的詳細資訊，請參閱︰

-   [API 管理中的原則](api-management-howto-policies.md)
-   [轉換 API](transform-api.md)
-   [原則參考文件](./api-management-policies.md)，取得原則陳述式及其設定的完整清單
-   [原則範例](policy-samples.md)
