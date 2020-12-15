---
title: 在 Azure Active Directory B2C 中定義自訂屬性 | Microsoft Docs
description: 在 Azure Active Directory B2C 中定義您應用程式的自訂屬性，以收集您客戶的相關資訊。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9759c1109c7be279520fa187bd3366bcac505d46
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503738"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中定義自訂屬性

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

在 [ [使用自訂原則新增宣告和自訂使用者輸入](configure-user-input.md) ] 文章中，您將瞭解如何使用內建的 [使用者設定檔屬性](user-profile-attributes.md)。 在本文中，您會在 Azure Active Directory B2C (Azure AD B2C) 目錄中啟用自訂屬性。 稍後，您可以在 [使用者流程](user-flow-overview.md) 或 [自訂原則](custom-policy-get-started.md) 中同時使用新的屬性做為自訂宣告。

您的 Azure AD B2C 目錄隨附一 [組內建屬性](user-profile-attributes.md)。 不過，您通常需要建立自己的屬性來管理您的特定案例，例如：

* 客戶面向的應用程式需要保存 **LoyaltyId** 屬性。
* 身分識別提供者必須保存唯一的使用者識別碼 **uniqueUserGUID**。
* 自訂使用者旅程圖必須保存使用者的狀態（ **>migrationstatus**），以供其他邏輯運作。

Azure AD B2C 可讓您擴充儲存在每個使用者帳戶上的屬性集。 您也可以使用 [Microsoft Graph API](manage-user-accounts-graph-api.md) 來讀取和寫入這些屬性。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>建立自訂屬性

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在 Azure 入口網站的右上角切換到您的 Azure AD B2C 租用戶，確定您使用的目錄包含該租用戶。 選取您的訂用帳戶資訊，然後選取 [切換目錄]。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [使用者屬性]，然後選取 [新增]。
1. 提供自訂屬性的 **名稱** (例如 "ShoeSize")
1. 選擇 [資料類型]。 只有 **字串**、**布林值**，以及 **Int** 可供使用。
1. 選擇性地輸入 [描述]，以供參考。
1. 按一下頁面底部的 [新增]  。

自訂屬性現在可在 **使用者屬性** 清單中使用，並在您的使用者流程中使用。 自訂屬性只會在第一次用於任何使用者流程時建立，而不會在您將其新增至 **使用者屬性** 清單時建立。

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>在您的使用者流程中使用自訂屬性

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
1. 選取您的原則 (例如「B2C_1_SignupSignin」) 以開啟之。
1. 選取 [使用者屬性]，然後選取自訂屬性 (例如 "ShoeSize")。 按一下 **[儲存]** 。
1. 選取 [應用程式宣告]，然後選取自訂屬性。
1. 按一下 **[儲存]** 。

當您使用使用者流程建立新的使用者，而該使用者流程使用新建立的自訂屬性時，就可以在 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)中查詢物件。 或者，您可以使用使用者流程上的「 [執行使用者流程](./tutorial-create-user-flows.md) 」功能來確認客戶體驗。 現在您應該會在註冊期間收集的屬性清單中看到 **ShoeSize** ，其亦會在傳回至您應用程式的權杖中出現。

::: zone-end

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

## <a name="using-custom-attribute-with-ms-graph-api"></a>使用自訂屬性搭配 MS 圖形 API

Microsoft Graph API 支援以擴充屬性建立和更新使用者。 圖形 API 中的擴充屬性會使用慣例來命名 `extension_ApplicationClientID_attributename` ，其中是應用程式 `ApplicationClientID` **(用戶端) 識別碼** 的應用 `b2c-extensions-app` 程式。 請注意， **應用程式 (用戶端) 識別碼** ，因為它在延伸模組屬性名稱中表示不包含連字號。 例如：

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
``` 

::: zone pivot="b2c-custom-policy"

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

您可以在自訂原則中使用入口網站 UI 之前或之後，使用入口網站 UI 建立這些屬性。 當您在入口網站中建立屬性 **loyaltyId** 時，您必須參考它，如下所示：

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

::: zone-end

## <a name="next-steps"></a>後續步驟

遵循如何 [使用自訂原則新增宣告和自訂使用者輸入](configure-user-input.md)的指引。 此範例會使用內建的宣告「city」。 若要使用自訂屬性，請將 ' city ' 取代為您自己的自訂屬性。