---
title: REST API 宣告交換-Azure Active Directory B2C
description: 將 REST API 宣告交換新增至 Active Directory B2C 中的自訂原則。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1caf0b3f6396cc008e77bb96e686fe78f462850b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849093"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中將 REST API 宣告交換新增至自訂原則

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

您可以在 Azure Active Directory B2C （Azure AD B2C）中，將與 RESTful API 的互動新增至您的[自訂原則](custom-policy-overview.md)。 本文說明如何建立與 RESTful 服務互動的 Azure AD B2C 使用者旅程圖。

互動包括 REST API 宣告和 Azure AD B2C 之間的宣告交換資訊。 宣告交換具有下列特性：

- 可以設計成協調流程步驟。
- 可以觸發外部動作。 例如，它可以在外部資料庫中記錄一個事件。
- 可用來擷取值，然後將它存放在使用者資料庫中。
- 可以變更執行流程。

本文中所述的案例包含下列動作：

1. 查閱外部系統中的使用者。
2. 取得註冊該使用者的城市。
3. 以宣告形式將該屬性傳回應用程式。

## <a name="prerequisites"></a>必要條件

- 完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。
- 要互動的 REST API 端點。 本文使用簡單的 Azure 函數做為範例。 若要建立 Azure 函式，請參閱[在 Azure 入口網站中建立您的第一個](../azure-functions/functions-create-first-azure-function.md)函式。

## <a name="prepare-the-api"></a>準備 API

在本節中，您會準備 Azure 函式以接收 `email`的值，然後傳回可供 Azure AD B2C 作為宣告之 `city` 的值。

變更您所建立之 Azure 函式的 .csx 檔案，以使用下列程式碼：

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>設定宣告交換

技術設定檔提供宣告交換的設定。

開啟*TrustFrameworkExtensions*檔案，並在**ClaimsProviders**元素內新增下列**ClaimsProvider** xml 元素。

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**InputClaims**元素會定義傳送至 REST 服務的宣告。 在此範例中，宣告 `givenName` 的值會傳送至 REST 服務，做為宣告 `email`。 **OutputClaims**元素會定義預期來自 REST 服務的宣告。

上述的批註 `AuthenticationType` 和 `AllowInsecureAuthInProduction` 指定當您移至生產環境時應該進行的變更。 若要瞭解如何保護您的 RESTful Api 以用於生產環境，請參閱使用基本驗證和[安全 RESTful api 搭配憑證驗證](secure-rest-api-dotnet-certificate-auth.md)來[保護 RESTful api](secure-rest-api-dotnet-basic-auth.md) 。

## <a name="add-the-claim-definition"></a>新增宣告定義

在**BuildingBlocks**元素內新增 `city` 的定義。 您可以在 TrustFrameworkExtensions.xml 檔案開頭處找到此元素。

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>新增協調流程步驟

有許多使用案例都可將 REST API 呼叫用來作為協調流程步驟。 作為協調流程步驟，它可在使用者成功完成工作 (例如首次註冊) 後作為外部系統的更新，或作為設定檔更新以讓資訊保持同步。 在此情況下，它會用來加強設定檔編輯之後提供給應用程式的資訊。

將步驟新增至設定檔編輯使用者旅程圖。 使用者通過驗證後（下列 XML 中的協調流程步驟1-4），且使用者已提供更新的設定檔資訊（步驟5）。 將設定檔編輯使用者旅程圖 XML 程式碼從*trustframeworkbase.xml*複製到**UserJourneys**元素內的*TrustFrameworkExtensions。* 然後在步驟6進行修改。

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

使用者旅程圖的最終 XML 看起來應該像下列範例：

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>新增宣告

編輯*profileedit.xml* ，並將 `<OutputClaim ClaimTypeReferenceId="city" />` 新增至**OutputClaims**元素。

加入新的宣告之後，技術設定檔看起來就像下面這個範例：

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>上傳您的變更並測試

1. 選擇性繼續之前，請先儲存檔案的現有版本（透過下載）。
2. 上傳*TrustFrameworkExtensions*和*profileedit.xml* ，然後選取以覆寫現有的檔案。
3. 選取 [ **B2C_1A_ProfileEdit**]。
4. 針對 [**選取應用程式**]，在自訂原則的 [總覽] 頁面上，選取您先前註冊之名為*webapp1*的 web 應用程式。 請確定 [**回復 URL** ] 是 `https://jwt.ms`。
4. 選取 [**立即執行**]。 使用您的帳號憑證登入，然後按一下 [**繼續**]。

如果所有專案都已正確設定，則權杖會包含新的宣告 `city`，其值 `Redmond`。

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>後續步驟

您也可以將互動設計成驗證設定檔。 如需詳細資訊，請參閱[逐步解說︰將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中以作為使用者輸入的驗證](custom-policy-rest-api-claims-validation.md)。

[修改設定檔編輯以從使用者收集其他資訊](custom-policy-custom-attributes.md)

[參考： RESTful 技術設定檔](restful-technical-profile.md)

若要瞭解如何保護您的 Api，請參閱下列文章：

* [使用基本驗證 (使用者名稱和密碼) 保護您的 RESTful API](secure-rest-api-dotnet-basic-auth.md)
* [使用用戶端憑證保護您的 RESTful API](secure-rest-api-dotnet-certificate-auth.md)
