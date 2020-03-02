---
title: 讓我保持登入 Azure Active Directory B2C
description: 了解如何在 Azure Active Directory B2C 中啟用「讓我保持登入 (KMSI)」。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 84ba68c97f69872e39121915a6edf23aa029fa75
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161681"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中啟用「讓我保持登入 (KMSI)」

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

您可以為 web 和原生應用程式的使用者啟用 [讓我保持登入（KMSI）] 功能，以便在 Azure Active Directory B2C （Azure AD B2C）目錄中具有本機帳戶。 這項功能會將存取權授與您的應用程式，而不會提示他們重新輸入其使用者名稱和密碼。 當使用者登出時，即會撤銷此存取權。

使用者應該不要在公用電腦上啟用此選項。

![顯示 [讓我保持登入] 核取方塊的註冊登入頁面範例](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Prerequisites

- 設定為允許本機帳戶登入的 Azure AD B2C 租使用者。 外部識別提供者帳戶不支援 KMSI。
- 完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。

## <a name="configure-the-page-identifier"></a>設定頁面識別碼 

若要啟用 KMSI，請將內容定義 `DataUri` 元素設定為[頁面識別碼](contentdefinitions.md#datauri)`unifiedssp` 和[頁面版本](page-layout.md) *1.1.0*或更新版本。

1. 開啟原則的擴充檔案。 例如， <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em> 。 此擴充檔案是自訂原則入門套件中包含的其中一個原則檔案，您應該已在必要條件中取得這些檔案，以[開始使用自訂原則](custom-policy-get-started.md)。
1. 搜尋 **BuildingBlocks** 元素。 如果此元素不存在，請加以新增。
1. 將**ContentDefinitions**元素新增至原則的**BuildingBlocks**元素。

    您的自訂原則看起來應該像下列程式碼片段：

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```
    
1. 儲存擴充檔案。



## <a name="configure-a-relying-party-file"></a>設定信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 開啟您的自訂原則檔案。 例如 *SignUpOrSignin.xml*。
1. 如果尚未存在，請將 `<UserJourneyBehaviors>` 子節點加入至 [`<RelyingParty>`] 節點。 它必須緊接在 `<DefaultUserJourney ReferenceId="User journey Id" />`之後，例如： `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`。
1. 新增下列節點作為 `<UserJourneyBehaviors>` 元素的子節點。

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** -指出會話如何擴充 `SessionExpiryInSeconds` 和 `KeepAliveInDays`中指定的時間。 `Rolling` 值（預設）表示每次使用者執行驗證時，都會擴充會話。 `Absolute` 值表示在指定的時間週期之後，會強制使用者重新驗證。
 
    - **SessionExpiryInSeconds** -未啟用 [*讓我保持登入*] 時會話 cookie 的存留期，或如果使用者未選取 [*讓我保持登入*]。 會話在通過 `SessionExpiryInSeconds` 或瀏覽器關閉之後就會過期。
 
    - **KeepAliveInDays** -已啟用 [*讓我保持*登入] 且使用者選取 [*讓我保持登入*] 時會話 cookie 的存留期。  `KeepAliveInDays` 的值優先于 `SessionExpiryInSeconds` 值，並規定會話到期時間。 如果使用者關閉瀏覽器後再重新開啟，他們仍然可以在 KeepAliveInDays 期間內以無訊息模式登入。
    
    如需詳細資訊，請參閱[使用者旅程圖行為](relyingparty.md#userjourneybehaviors)。
 
我們建議您將 SessionExpiryInSeconds 的值設定為短時間（1200秒），而 KeepAliveInDays 的值可以設定為相對較長的期間（30天），如下列範例所示：

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

4. 儲存變更然後上傳檔案。
5. 若要測試您上傳的自訂原則，在 Azure 入口網站中，移至原則視窗，然後選取 [立即執行]。

您可以在[這裡](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)找到範例原則。
