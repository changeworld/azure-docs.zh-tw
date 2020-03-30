---
title: 將您自己的屬性添加到自訂策略
titleSuffix: Azure AD B2C
description: 逐步解說如何使用擴充屬性 (Property) 和自訂屬性 (Attribute)，並將其包含於使用者介面中。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc5204518cb6e801ba661aecd5498a501122225f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473654"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure 活動目錄 B2C：在自訂設定檔策略中啟用自訂屬性

在["添加聲明"中，並使用自訂策略自訂使用者輸入](custom-policy-configure-user-input.md)一文中，您將瞭解如何使用內置[使用者設定檔屬性](user-profile-attributes.md)。 在本文中，您將在 Azure 活動目錄 B2C （Azure AD B2C） 目錄中啟用自訂屬性。 稍後，您可以在[使用者流](user-flow-overview.md)或[自訂策略](custom-policy-get-started.md)中同時使用新屬性作為自訂聲明。

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Prerequisites

依照 [Azure Active Directory B2C：開始使用自訂原則](custom-policy-get-started.md)一文中的步驟執行。

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>使用自訂屬性收集有關客戶的資訊 

Azure AD B2C 目錄附帶一[組內置屬性](user-profile-attributes.md)。 但是，您通常需要創建自己的屬性來管理特定方案，例如：在以下情況下：

* 面向客戶的應用程式需要保留一個**LoyalId**屬性。
* 標識提供程式具有唯一的使用者識別碼 **，唯一使用者 GUID，** 必須保留該識別碼。
* 自訂使用者旅程需要保留使用者的狀態 **，遷移狀態**，以便對其他邏輯進行操作。

Azure AD B2C 允許您擴展存儲在每個使用者帳戶的屬性集。 您還可以使用[Microsoft 圖形 API](manage-user-accounts-graph-api.md)讀取和寫入這些屬性。

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C 擴展應用

擴充屬性只能在應用程式物件上註冊，即使它們可能包含使用者的資料。 擴充屬性附加到稱為 b2c 擴展應用程式的應用程式。 不要修改此應用程式，因為它被 Azure AD B2C 用於存儲使用者資料。 您可以在 Azure AD B2C、應用註冊下找到此應用程式。

「擴充屬性」**、「自訂屬性」** 及「自訂宣告」** 等術語在本文內容中係指相同的動作。 名稱會根據內容 (例如應用程式、物件或原則) 而有所不同。

## <a name="get-the-application-properties"></a>獲取應用程式屬性

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]**** 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 選擇**應用註冊（預覽），** 然後選擇 **"所有應用程式**"。
1. 選擇`b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`應用程式。
1. 將下列識別碼複製到剪貼簿並加以儲存：
    * **應用程式 ID**. 範例： `11111111-1111-1111-1111-111111111111`.
    * **物件識別碼**. 範例： `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>修改自訂策略

要在策略中啟用自訂屬性，請在 AAD 通用技術設定檔中繼資料中提供**應用程式 ID**和應用程式**物件識別碼。** *AAD-通用*技術設定檔位於基本[Azure 活動目錄](active-directory-technical-profile.md)技術設定檔中，並支援 Azure AD 使用者管理。 其他 Azure AD 技術設定檔包括 AAD 通用以利用其配置。 覆蓋擴展檔中的 AAD 通用技術設定檔。

1. 打開策略的擴展檔。 例如， <em> `SocialAndLocalAccounts/` </em>.
1. 尋找 ClaimsProviders 元素。 向聲明提供程式元素添加新的聲明提供程式。
1. 替換為`ApplicationObjectId`以前記錄的物件識別碼。 然後替換為`ClientId`以前記錄在以下程式碼片段的應用程式 ID。

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
      <TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>上傳自訂策略

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 通過在頂部功能表中選擇**目錄 + 訂閱**篩選器並選擇包含 Azure AD B2C 租戶的目錄，請確保使用的目錄包含 Azure AD 租戶。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
4. 選取 [識別體驗架構]****。
5. 選擇 **"上載自訂策略**"，然後上載您更改的 TrustFramework 擴展.xml 策略檔。

> [!NOTE]
> 第一次 Azure AD 技術設定檔將聲明保存到目錄時，它會檢查自訂屬性是否存在。 如果沒有，它將創建自訂屬性。  

## <a name="create-a-custom-attribute-through-azure-portal"></a>通過 Azure 門戶創建自訂屬性

相同的擴充屬性在內置策略和自訂策略之間共用。 通過門戶體驗添加自訂屬性時，將使用每個 B2C 租戶中存在的**b2c 擴展應用**來註冊這些屬性。

您可以在自訂策略中使用門戶 UI 來創建這些屬性。 按照有關如何在 Azure[活動目錄 B2C 中定義自訂屬性](user-flow-custom-attributes.md)的指導。 在門戶中創建屬性 **"忠誠 Id"** 時，必須引用如下：

|名稱     |使用於 |
|---------|---------|
|`extension_loyaltyId`  | 自訂原則|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

下面的示例演示了在 Azure AD B2C 自訂策略聲明定義中使用自訂屬性。

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

下面的示例演示了在技術設定檔、輸入、輸出和持久聲明中使用 Azure AD B2C 自訂策略中的自訂屬性。

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

## <a name="use-a-custom-attribute-in-a-policy"></a>在策略中使用自訂屬性

按照有關如何[使用自訂策略添加聲明和自訂使用者輸入的指導](custom-policy-configure-user-input.md)。 此示例使用內置聲明"城市"。 要使用自訂屬性，請將"城市"替換為您自己的自訂屬性。


## <a name="next-steps"></a>後續步驟

深入了解：

- [Azure AD B2C 使用者設定檔屬性](user-profile-attributes.md)
- [擴充屬性定義](user-profile-attributes.md#extension-attributes)
- [使用微軟圖形管理 Azure AD B2C 使用者帳戶](manage-user-accounts-graph-api.md)
