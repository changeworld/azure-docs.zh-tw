---
title: 在 Azure 活動目錄 B2C 中保持登錄
description: 了解如何在 Azure Active Directory B2C 中啟用「讓我保持登入 (KMSI)」。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a0de94cdce1d7f0e9da9d2844b300956ad6f6970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330848"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中啟用「讓我保持登入 (KMSI)」

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

您可以為在 Azure 活動目錄 B2C （Azure AD B2C） 目錄中具有本地帳戶的 Web 和本機應用程式的使用者啟用"保持我登錄 （KMSI）"功能。 此功能授予返回應用程式的使用者的存取權限，而不提示他們重新輸入使用者名和密碼。 當使用者登出時，即會撤銷此存取權。

使用者應該不要在公用電腦上啟用此選項。

![顯示"讓我登錄"核取方塊的示例註冊登錄頁面](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Prerequisites

- 配置為允許本地帳戶登錄的 Azure AD B2C 租戶。 外部標識提供程式帳戶不支援 KMSI。
- 完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。

## <a name="configure-the-page-identifier"></a>配置頁面識別碼

要啟用 KMSI，將內容定義`DataUri`元素設置為[頁面識別碼](contentdefinitions.md#datauri)`unifiedssp`和[頁面版本](page-layout.md) *1.1.0*或以上。

1. 開啟原則的擴充檔案。 例如， <em> `SocialAndLocalAccounts/` </em>. 此擴展檔是自訂策略初學者包中包含的策略檔之一，您應該在先決條件中獲取，[從自訂策略開始](custom-policy-get-started.md)。
1. 搜尋 **BuildingBlocks** 元素。 如果此元素不存在，請加以新增。
1. 將**內容定義**元素添加到策略的**構建塊**元素。

    自訂策略應類似于以下程式碼片段：

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>將中繼資料添加到自斷言技術設定檔

要將 KMSI 核取方塊添加到註冊和登錄頁面，請將`setting.enableRememberMe`中繼資料設置為 false。 覆蓋擴展檔中的自斷言-本地帳戶Signin-電子郵件技術設定檔。

1. 尋找 ClaimsProviders 元素。 如果此元素不存在，請加以新增。
1. 將以下聲明提供程式添加到聲明提供程式元素：

```XML
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

## <a name="configure-a-relying-party-file"></a>配置依賴方檔

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 開啟您的自訂原則檔案。 例如 *SignUpOrSignin.xml*。
1. 如果不存在，請向`<UserJourneyBehaviors>``<RelyingParty>`節點添加子節點。 它必須立即位於`<DefaultUserJourney ReferenceId="User journey Id" />`之後，例如： `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`。
1. 新增下列節點作為 `<UserJourneyBehaviors>` 元素的子節點。

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **會話過期類型**- 指示如何在 和`SessionExpiryInSeconds``KeepAliveInDays`中指定的時間延長會話。 該`Rolling`值（預設值）表示每次使用者執行身份驗證時都會擴展會話。 該`Absolute`值指示使用者在指定的時間段後強制重新進行身份驗證。

    - **會話過期時間**-*保持登錄*時會話 Cookie 的存留期未啟用，或者如果使用者未選擇*保持我登錄*。 會話在通過後`SessionExpiryInSeconds`過期，或者瀏覽器關閉。

    - **保持AliveInDays** -*保持登錄*時會話 Cookie 的存留期已啟用，使用者選擇*保持我登錄*。  的值`KeepAliveInDays`優先于`SessionExpiryInSeconds`值，並指示會話到期時間。 如果使用者關閉瀏覽器並在以後重新打開瀏覽器，則只要瀏覽器處於"保持 AliveInDays"時間段內，他們仍然可以靜默登錄。

    有關詳細資訊，請參閱[使用者旅程行為](relyingparty.md#userjourneybehaviors)。

我們建議您將 Session 過期的"秒"值設置為短時間（1200 秒），而 KeepAliveInDays 的值可以設置為相對長的週期（30 天），如以下示例所示：

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

## <a name="test-your-policy"></a>測試您的原則

1. 儲存變更然後上傳檔案。
1. 要測試您上傳的自訂策略，請在 Azure 門戶中轉到策略頁，然後選擇 **"立即運行**"。
1. 鍵入您的**使用者名和密碼**，**password**選擇 **"保持我登錄**"，然後按一下 **"登錄**"。
1. 回到 Azure 入口網站。 轉到策略頁，然後選擇 **"複製"** 以複製登錄 URL。
1. 在瀏覽器網址列中，刪除`&prompt=login`查詢字串參數，該參數強制使用者在該請求上輸入其憑據。
1. 在瀏覽器中，按一下"**轉到**"。 現在，Azure AD B2C 將發出訪問權杖，而不會提示您再次登錄。 

## <a name="next-steps"></a>後續步驟

[在此處](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)查找示例策略。
