---
title: 將商標新增至組織的登入頁面
titleSuffix: Azure AD B2C
description: 瞭解如何將貴組織的商標新增至 Azure Active Directory B2C 頁面。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111193"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>將商標新增至組織的 Azure Active Directory B2C 頁面

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

您可以使用 Azure Active Directory [公司商標](../active-directory/fundamentals/customize-branding.md)來自訂您的 Azure AD B2C 頁面，以及橫幅標誌、背景影像和背景色彩。 公司商標包括註冊、登入、設定檔編輯和密碼重設。 

> [!TIP]
> 若要自訂使用者流程頁面的其他層面（除了橫幅標誌、背景影像或背景色彩），請參閱如何 [使用 HTML 範本自訂 UI](customize-ui-with-html.md)。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


若要自訂您的使用者流程頁面，請先在 Azure Active Directory 中設定公司商標，然後在 Azure AD B2C 使用者流程的頁面配置中加以啟用。

## <a name="configure-company-branding"></a>設定公司商標

一開始先設定 **公司商標** 內的橫幅標誌、背景影像和背景色彩。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 在 [ **管理**] 底下，選取 [ **公司商標**]。
1. 遵循 [將商標新增至組織的 Azure Active Directory 登入頁面](../active-directory/fundamentals/customize-branding.md)中的步驟。

當您在 Azure AD B2C 中設定公司商標時，請記住下列事項：

* Azure AD B2C 中的公司商標目前僅限 **背景影像**、 **橫幅標誌** 和 **背景色彩** 自訂。 公司商標窗格中的其他屬性（例如，在 [ **Advanced settings**] 中）則 *不受支援*。
* 在您的使用者流程頁面中，背景色彩會在載入背景影像之前顯示。 我們建議您選擇與背景影像中的色彩緊密相符的背景色彩，以提供更流暢的載入體驗。
* 橫幅標誌會顯示在使用者起始註冊使用者流程時傳送給使用者的驗證電子郵件中。

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>在使用者流程頁面中啟用商標

設定公司商標之後，請在您的使用者流程中啟用。

1. 在 Azure 入口網站的左側功能表中，選取 [ **Azure AD B2C**]。
1. 在 [原則] 底下，選取 [使用者流程 (原則)]。
1. 選取您要啟用公司商標的使用者流程。 標準登 *入* 和標準 *設定檔編輯* 使用者流程類型 **不支援** 公司商標。
1. 在 [ **自訂**] 底下，選取 [ **頁面配置**]，然後選取您想要品牌的版面配置。 例如，選取 [ **統一註冊或登入] 頁面**。
1. 針對 **(Preview) 的頁面配置版本**，請選擇 [版本 **1.2.0** ] 或更新版本。
1. 選取 [儲存]。

如果您想要在使用者流程中設定所有頁面的品牌，請在使用者流程中設定每個頁面配置的頁面配置版本。

![Azure 入口網站 Azure AD B2C 中的頁面配置選取專案](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>選取頁面配置

若要啟用公司商標，您需要 [](contentdefinitions.md#migrating-to-page-layout)為 `contract` 自訂原則中的 *所有* 內容定義，定義頁面版本的頁面配置版本。 值的格式必須包含 " `contract` _urn： com： microsoft： aad： b2c： elements：**contract**:p age： version_。 若要在自訂原則中指定使用舊 **DataUri** 值的頁面配置。

瞭解如何使用頁面版本 [遷移至頁面配置](contentdefinitions.md#migrating-to-page-layout) 。

下列範例會顯示內容定義識別碼和對應的 **DataUri** 與頁面合約： 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

下列範例會在使用海洋 Blue 範本的 *註冊和登入* 使用者流程頁面上，顯示自訂橫幅標誌和背景影像：

![由 Azure AD B2C 提供的品牌註冊/登入頁面](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>使用自訂 HTML 中的公司商標資產

若要在 [自訂的 HTML](customize-ui-with-html.md)中使用您的公司商標資產，請在標記之外新增下列標記 `<div id="api">` ：

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

影像來源會取代為背景影像和橫幅標誌的影像來源。

## <a name="next-steps"></a>後續步驟

在 Azure Active Directory B2C 中，您可以從 [自訂應用程式的使用者介面](customize-ui-with-html.md)，以取得如何自訂應用程式使用者介面的詳細資訊。