---
title: 使用 Application Insights 追蹤使用者行為
titleSuffix: Azure AD B2C
description: 瞭解如何使用自訂原則，從 Azure AD B2C 使用者旅程啟用 Application Insights 中的事件記錄檔。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 646e3e0d68846013d656627a4ef6ef1fb1e11e09
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846766"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>使用 Application Insights 在 Azure Active Directory B2C 中追蹤使用者行為

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

當您使用 Azure Active Directory B2C （Azure AD B2C）搭配 Azure 應用程式 Insights 時，您可以為使用者旅程取得詳細的自訂事件記錄檔。 在本文中，您將學會如何：

* 深入了解使用者行為。
* 在開發或實際執行時對您自己的原則進行疑難排解。
* 測量效能。
* 從 Application Insights 建立通知。

## <a name="how-it-works"></a>運作方式

Azure AD B2C 中的身分識別體驗架構納入了 `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0` 提供者。 它會使用提供給 Azure AD B2C 的檢測金鑰，將事件資料直接傳送到 Application Insights。

技術設定檔會使用此提供者來定義 Azure AD B2C 的事件。 這個設定檔會指定事件的名稱、所記錄的宣告及檢測金鑰。 為了張貼事件，會將技術設定檔新增為自訂使用者旅程圖中的 `orchestration step`。

Application Insights 可以使用相互關聯識別碼來記錄使用者工作階段，以此方式統一事件。 Application Insights 會在數秒內使事件和工作階段成為可用狀態，並提供許多視覺效果、匯出及分析工具。

## <a name="prerequisites"></a>必要條件

完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。 本文假設您使用自訂原則入門套件。 但您不一定要使用入門套件。

## <a name="create-an-application-insights-resource"></a>建立 Application Insights 資源

當您使用 Application Insights 搭配 Azure AD B2C 時，您只需要建立資源並取得檢測金鑰。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 請選取頂端功能表中的 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您訂用帳戶的目錄，以確定您使用的是包含 Azure 訂用帳戶的目錄。 此租用戶不是您的 Azure AD B2C 租用戶。
3. 選擇 Azure 入口網站左上角的 [建立資源]，然後搜尋並選取 [Application Insights]。
4. 按一下頁面底部的 [新增]。
5. 輸入資源的 [名稱]。
6. 針對 [應用程式類型]，選取 [ASP.NET Web 應用程式]。
7. 針對 [資源群組]，選取現有的群組或輸入新群組的名稱。
8. 按一下頁面底部的 [新增]。
4. 建立 Application Insights 資源後，開啟資源、展開 [基本資訊]，並複製檢測金鑰。

![Application Insights 概觀與檢測金鑰](./media/analytics-with-application-insights/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>新增 ClaimType 定義

從入門套件開啟 TrustFrameworkExtensions.xml 檔案，並將下列元素新增至 [BuildingBlocks](buildingblocks.md) 元素：

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="add-new-technical-profiles"></a>新增技術設定檔

技術設定檔可視為是 Azure AD B2C 身分識別體驗架構中的功能。 此資料表會定義技術設定檔，用來開啟工作階段並張貼事件。

| 技術設定檔 | Task |
| ----------------- | -----|
| AzureInsights-Common | 建立要包含於所有 Azure-Insights 技術設定檔中的一組通用參數。 |
| AzureInsights-SignInRequest | 收到登入要求之後，建立含有一組宣告的 "SignIn" 事件。 |
| AzureInsights-UserSignup | 當使用者觸發註冊/登入旅程圖中的註冊選項時，建立 UserSignup 事件。 |
| AzureInsights-SignInComplete | 將權杖傳送到信賴憑證者應用程式之後，記錄驗證已成功完成。 |

從入門套件將設定檔新增至 TrustFrameworkExtensions.xml 檔案。 將這些元素新增至 **ClaimsProviders** 元素：

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> 將 `AzureInsights-Common` 技術設定檔中的檢測金鑰變更為 Application Insights 資源所提供的 GUID。

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>新增技術設定檔作為協調流程步驟

呼叫 `Azure-Insights-SignInRequest` 作為協調流程的步驟 2，以追蹤收到的登入/註冊要求：

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

新增呼叫 `Azure-Insights-UserSignup` 的新步驟，*後面*緊接著 `SendClaims` 協調流程步驟。 當使用者選取註冊/登入旅程圖中的註冊按鈕時，就會觸發。

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

緊接在 `SendClaims` 協調流程步驟之後，呼叫 `Azure-Insights-SignInComplete`。 此步驟會顯示已成功完成的旅程圖。

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> 新增協調流程步驟之後，循序將步驟從 1 到 N 重新編號，不要略過任何整數。


## <a name="upload-your-file-run-the-policy-and-view-events"></a>上傳檔案、執行原則，並檢視事件

儲存並上傳 TrustFrameworkExtensions.xml 檔案。 然後，從您的應用程式或使用 Azure 入口網站中的**立即執行**呼叫信賴憑證者原則。 您的事件將在數秒內於 Application Insights 中變成可用狀態。

1. 在 Azure Active Directory 租用戶中開啟 **Application Insights** 資源。
2. 選取 [使用量] > [事件]。
3. 將 [期間] 設定為 [過去一小時內]，將 [間隔] 設定為 [3 分鐘]。  您可能需要選取 [重新整理] 才能檢視結果。

![Application Insights 使用量事件刀鋒視窗](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="next-steps"></a>後續步驟

將宣告類型和事件新增至您的使用者旅程圖，以符合您的需求。 您可以使用 [宣告解析程式](claim-resolver-overview.md) 或任何字串宣告類型，將 **輸入宣告** 元素新增至 Application Insights 事件，或新增至 AzureInsights-Common 技術設定檔來新增宣告。

- **ClaimTypeReferenceId** 是宣告類型的參考。
- **PartnerClaimType** 是出現在 Azure Insights 中的屬性名稱。 使用 `{property:NAME}` 的語法，其中 `NAME` 是新增至事件的屬性。
- **DefaultValue** 會使用任何字串值或宣告解析程式。

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

