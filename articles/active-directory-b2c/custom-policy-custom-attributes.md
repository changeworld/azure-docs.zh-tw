---
title: 將您自己的屬性新增至自訂原則
titleSuffix: Azure AD B2C
description: 逐步解說如何使用擴充屬性 (Property) 和自訂屬性 (Attribute)，並將其包含於使用者介面中。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9ec99558f3a168b770ad19fb4f6c811a31c44f08
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108871"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C：在自訂設定檔原則中啟用自訂屬性

在 [ [使用自訂原則新增宣告和自訂使用者輸入](configure-user-input.md) ] 文章中，您將瞭解如何使用內建的 [使用者設定檔屬性](user-profile-attributes.md)。 在本文中，您會在 Azure Active Directory B2C (Azure AD B2C) 目錄中啟用自訂屬性。 稍後，您可以在 [使用者流程](user-flow-overview.md) 或 [自訂原則](custom-policy-get-started.md) 中同時使用新的屬性做為自訂宣告。

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Prerequisites

依照 [Azure Active Directory B2C：開始使用自訂原則](custom-policy-get-started.md)一文中的步驟執行。

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>使用自訂屬性收集您的客戶相關資訊 

您的 Azure AD B2C 目錄隨附一 [組內建屬性](user-profile-attributes.md)。 不過，您通常需要建立自己的屬性來管理您的特定案例，例如：

* 客戶面向的應用程式需要保存 **LoyaltyId** 屬性。
* 身分識別提供者必須保存唯一的使用者識別碼 **uniqueUserGUID**。
* 自訂使用者旅程圖必須保存使用者的狀態（ **>migrationstatus**），以供其他邏輯運作。

Azure AD B2C 可讓您擴充儲存在每個使用者帳戶上的屬性集。 您也可以使用 [Microsoft Graph API](manage-user-accounts-graph-api.md) 來讀取和寫入這些屬性。

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C 擴充功能應用程式

擴充屬性只能在應用程式物件上註冊，即使它們可能包含使用者的資料也一樣。 擴充屬性會附加至名為 b2c-extensions-app 的應用程式。 請勿修改此應用程式，因為 Azure AD B2C 會使用此應用程式來儲存使用者資料。 您可以在 [應用程式註冊 Azure AD B2C] 下找到此應用程式。

「擴充屬性」、「自訂屬性」及「自訂宣告」等術語在本文內容中係指相同的動作。 名稱會根據內容 (例如應用程式、物件或原則) 而有所不同。

## <a name="get-the-application-properties"></a>取得應用程式屬性

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 **應用程式註冊**，然後選取 [ **所有應用程式**]。
1. 選取 `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` 應用程式。
1. 將下列識別碼複製到剪貼簿並加以儲存：
    * **應用程式識別碼**。 範例： `11111111-1111-1111-1111-111111111111`.
    * **物件識別碼**。 範例： `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>修改您的自訂原則

若要在您的原則中啟用自訂屬性，請在 AAD-Common 技術設定檔中繼資料中提供 **應用程式識別碼** 和應用程式 **物件識別碼** 。 *AAD 一般* 技術設定檔可在基底 [Azure Active Directory](active-directory-technical-profile.md)技術設定檔中找到，並提供 Azure AD 使用者管理的支援。 其他 Azure AD 技術設定檔包含 AAD-Common，以利用其設定。 覆寫擴充檔中的 AAD-Common 技術設定檔。

1. 開啟您原則的擴充檔。 例如，<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>。
1. 尋找 ClaimsProviders 元素。 將新的 ClaimsProvider 加入至 Claimsprovider 元素。
1. 取代 `ApplicationObjectId` 為您先前記錄的物件識別碼。 然後 `ClientId` 以您先前在下列程式碼片段中記錄的應用程式識別碼取代。

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>上傳您的自訂原則

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取頂端功能表中的 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租使用者的目錄，以確定您使用的是包含 Azure AD 租使用者的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
4. 選取 [識別體驗架構]。
5. 選取 **[上傳自訂原則**]，然後上傳您變更的 TrustFrameworkExtensions.xml 原則檔。

> [!NOTE]
> 第一次 Azure AD 技術設定檔將宣告保存至目錄時，它會檢查自訂屬性是否存在。 如果沒有，則會建立自訂屬性。  

## <a name="create-a-custom-attribute-through-azure-portal"></a>透過 Azure 入口網站建立自訂屬性

在內建和自訂原則之間共用相同的延伸模組屬性。 當您透過入口網站體驗新增自訂屬性時，系統會使用存在於每個 B2C 租使用者中的 **b2c 延伸模組應用程式** 來註冊這些屬性。

您可以在自訂原則中使用入口網站 UI 之前或之後，使用入口網站 UI 建立這些屬性。 遵循如何 [在 Azure Active Directory B2C 中定義自訂屬性](user-flow-custom-attributes.md)的指引。 當您在入口網站中建立屬性 **loyaltyId** 時，您必須參考它，如下所示：

|名稱     |使用於 |
|---------|---------|
|`extension_loyaltyId`  | 自訂原則|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

下列範例示範如何在 Azure AD B2C 自訂原則宣告定義中使用自訂屬性。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

下列範例示範如何在技術設定檔、輸入、輸出和保存的宣告中，于 Azure AD B2C 自訂原則中使用自訂屬性。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>在原則中使用自訂屬性

遵循如何 [使用自訂原則新增宣告和自訂使用者輸入](configure-user-input.md)的指引。 此範例會使用內建的宣告「city」。 若要使用自訂屬性，請將 ' city ' 取代為您自己的自訂屬性。


## <a name="next-steps"></a>後續步驟

深入了解：

- [Azure AD B2C 使用者設定檔屬性](user-profile-attributes.md)
- [擴充屬性定義](user-profile-attributes.md#extension-attributes)
- [使用 Microsoft Graph 管理 Azure AD B2C 的使用者帳戶](manage-user-accounts-graph-api.md)
