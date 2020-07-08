---
title: 讓我保持登入 Azure Active Directory B2C
description: 了解如何在 Azure Active Directory B2C 中啟用「讓我保持登入 (KMSI)」。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: de5dd051804f3a0a7d1b0d32b998262af13e8926
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389185"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中啟用「讓我保持登入 (KMSI)」

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

您可以為 web 和原生應用程式的使用者啟用 [讓我保持登入（KMSI）] 功能，以便在 Azure Active Directory B2C （Azure AD B2C）目錄中具有本機帳戶。 這項功能會將存取權授與您的應用程式，而不會提示他們重新輸入其使用者名稱和密碼。 當使用者登出時，即會撤銷此存取權。

使用者應該不要在公用電腦上啟用此選項。

![顯示 [讓我保持登入] 核取方塊的註冊登入頁面範例](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>必要條件

- 設定為允許本機帳戶登入的 Azure AD B2C 租使用者。 外部識別提供者帳戶不支援 KMSI。
- 完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。

## <a name="configure-the-page-identifier"></a>設定頁面識別碼

若要啟用 KMSI，請將內容定義專案設定 `DataUri` 為[頁面識別碼](contentdefinitions.md#datauri) `unifiedssp` 和[頁面版本](page-layout.md) *1.1.0*或更新版本。

1. 開啟原則的擴充檔案。 例如，<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>。 此擴充檔案是自訂原則入門套件中包含的其中一個原則檔案，您應該已在必要條件中取得這些檔案，以[開始使用自訂原則](custom-policy-get-started.md)。
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

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>將中繼資料新增至自我判斷技術設定檔

若要將 [KMSI] 核取方塊新增至註冊和登入頁面，請將 `setting.enableRememberMe` 中繼資料設定為 true。 覆寫延伸模組檔案中的 SelfAsserted-LocalAccountSignin-電子郵件技術設定檔。

1. 尋找 ClaimsProviders 元素。 如果此元素不存在，請加以新增。
1. 將下列宣告提供者新增至 ClaimsProviders 元素：

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. 儲存擴充檔案。

## <a name="configure-a-relying-party-file"></a>設定信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 開啟您的自訂原則檔案。 例如 *SignUpOrSignin.xml*。
1. 如果尚未存在，請將 `<UserJourneyBehaviors>` 子節點加入至 `<RelyingParty>` 節點。 它必須緊接在之後 `<DefaultUserJourney ReferenceId="User journey Id" />` ，例如： `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` 。
1. 新增下列節點作為 `<UserJourneyBehaviors>` 元素的子節點。

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** -指出會話如何透過和中指定的時間擴充 `SessionExpiryInSeconds` `KeepAliveInDays` 。 `Rolling`值（預設）表示每次使用者執行驗證時，都會擴充會話。 `Absolute`值表示在指定的時間週期之後，會強制使用者重新驗證。

    - **SessionExpiryInSeconds** -未啟用 [*讓我保持登入*] 時會話 cookie 的存留期，或如果使用者未選取 [*讓我保持登入*]。 會話會在通過後過期 `SessionExpiryInSeconds` ，或關閉瀏覽器。

    - **KeepAliveInDays** -已啟用 [*讓我保持*登入] 且使用者選取 [*讓我保持登入*] 時會話 cookie 的存留期。  的值 `KeepAliveInDays` 優先于 `SessionExpiryInSeconds` 值，而且會指示會話到期時間。 如果使用者關閉瀏覽器後再重新開啟，他們仍然可以在 KeepAliveInDays 期間內以無訊息模式登入。

    如需詳細資訊，請參閱[使用者旅程圖行為](relyingparty.md#userjourneybehaviors)。

我們建議您將 SessionExpiryInSeconds 的值設定為短時間（1200秒），而 KeepAliveInDays 的值可以設定為相對較長的期間（30天），如下列範例所示：

```xml
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

## <a name="test-your-policy"></a>測試您的原則

1. 儲存變更然後上傳檔案。
1. 若要測試您上傳的自訂原則，請在 Azure 入口網站中，移至 [原則] 頁面，然後選取 [**立即執行**]。
1. 輸入您的使用者**名稱**和**密碼**，選取 [**讓我保持登入**]，然後按一下 [登**入**]。
1. 回到 Azure 入口網站。 移至 [原則] 頁面，然後選取 [**複製**] 以複製登入 URL。
1. 在瀏覽器網址列中，移除 `&prompt=login` 查詢字串參數，這會強制使用者在該要求上輸入其認證。
1. 在瀏覽器中，按一下 [**移至**]。 現在 Azure AD B2C 將會發出存取權杖，而不會提示您再次登入。 

## <a name="next-steps"></a>後續步驟

在[這裡](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)尋找範例原則。
