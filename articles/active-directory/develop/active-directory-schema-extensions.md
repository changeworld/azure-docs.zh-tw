---
title: 在宣告中使用 Azure AD 架構延伸屬性
titleSuffix: Microsoft identity platform
description: 描述如何使用目錄架構延伸模組屬性，將使用者資料傳送至權杖宣告中的應用程式。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 8861e641f5ee6a10576425a7702ba02da297a0bf
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631268"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>在宣告中使用目錄架構延伸模組屬性

目錄架構延伸模組屬性提供一種方式，可將其他資料儲存在 Azure Active Directory 的使用者物件和其他目錄物件，例如群組、租使用者詳細資料、服務主體。  只有使用者物件的擴充屬性可以用來發出宣告給應用程式。 本文說明如何使用目錄架構延伸模組屬性，將使用者資料傳送至權杖宣告中的應用程式。

> [!NOTE]
> Microsoft Graph 提供兩個其他擴充機制來自訂繪圖物件。 這些稱為 Microsoft Graph 開放式擴充功能和 Microsoft Graph 架構延伸。 如需詳細資訊，請參閱 [Microsoft Graph 檔](/graph/extensibility-overview) 。 使用這些功能儲存在 Microsoft Graph 物件上的資料，不會作為權杖中宣告的來源使用。

目錄架構延伸模組屬性一律與租使用者中的應用程式相關聯，並由應用程式的 *applicationId* 名稱參考。

目錄架構延伸模組屬性的識別碼是 *Extension_xxxxxxxxx_AttributeName*格式。  其中 *xxxxxxxxx* 是為其定義擴充功能的應用程式的 *applicationId* 。

## <a name="registering-and-using-directory-schema-extensions"></a>註冊和使用目錄架構延伸模組
您可以透過下列兩種方式之一來註冊和填入目錄架構延伸模組屬性：

- 藉由設定 AD Connect 來建立它們，並將資料從內部部署 AD 同步至其中。 請參閱 [Azure AD Connect 同步目錄擴充](../hybrid/how-to-connect-sync-feature-directory-extensions.md)功能。
- 藉由使用 Microsoft Graph 註冊、設定的值，以及從 [架構延伸](/graph/extensibility-overview)讀取。 [PowerShell Cmdlet](/powershell/azure/active-directory/using-extension-attributes-sample) 也可供使用。

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>從使用 AD Connect 建立的目錄架構延伸模組屬性發出宣告與資料
使用 AD Connect 建立和同步處理的目錄架構延伸模組屬性，一律會與 AD Connect 所使用的應用程式識別碼產生關聯。 您可以使用它們作為宣告的來源，方法是在入口網站 UI 中的 [ **企業應用** 程式] 設定中，針對使用資源庫或非資源庫應用程式設定體驗註冊的 SAML 應用 **程式，以及**透過應用程式註冊體驗註冊之應用程式的宣告對應原則，將它們設定為宣告。  一旦透過 AD Connect 建立的目錄延伸模組屬性在目錄中，它就會顯示在 SAML SSO 宣告設定 UI 中。

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>使用 Graph 或 PowerShell 以針對應用程式所建立之目錄架構延伸屬性的資料發出宣告
如果使用 Microsoft Graph 或 PowerShell (透過應用程式) 的初始設定或布建步驟，為應用程式登錄目錄架構延伸模組屬性，則可以在 Azure Active Directory 中設定相同的應用程式，以便在使用者登入時從宣告中的使用者物件接收該屬性中的資料。  您可以設定應用程式，以在使用 [選擇性宣告](active-directory-optional-claims.md#configuring-directory-extension-optional-claims)在該相同應用程式上註冊的目錄架構延伸中接收資料。  這些可以在應用程式資訊清單中設定。  這可讓多租使用者應用程式登錄目錄架構延伸模組屬性，以供自己使用。 當應用程式布建到租使用者時，相關聯的目錄架構延伸模組就會變成可在該租使用者中的使用者上進行設定，並可供使用。  在租使用者中設定並授與同意之後，就可以使用它來透過 graph 儲存和取出資料，並對應到 Microsoft 身分識別平臺發出給應用程式之權杖中的宣告。

您可以針對任何應用程式註冊和填入目錄架構延伸模組屬性。

如果應用程式需要從不同應用程式上註冊的延伸模組屬性的資料傳送宣告，則必須使用 [宣告對應原則](active-directory-claims-mapping.md) 將擴充屬性對應至宣告。  管理目錄架構延伸屬性的常見模式是建立一個應用程式，特別是您需要的所有架構延伸的註冊點。  它不一定是真正的應用程式，而這項技術表示所有延伸模組的名稱都有相同的應用程式識別碼。

例如，以下是從 OAuth/OIDC 權杖中的目錄架構延伸屬性發出單一宣告的宣告對應原則：

```json
{
    "ClaimsMappingPolicy": {
        "Version": 1,
        "IncludeBasicClaimSet": "false",
        "ClaimsSchema": [{
                "Source": "User",
                "ExtensionID": "extension_xxxxxxx_test",
                "JWTClaimType": "http://schemas.contoso.com/identity/claims/exampleclaim"
            },
        ]
    }
}
```

其中 *>xxxxxxx* 是註冊擴充功能的應用程式識別碼。

> [!TIP]
> 在物件上設定目錄延伸屬性時，大小寫一致性相當重要。  在設定時，擴充屬性名稱不區分大小寫，但權杖服務從目錄讀取時，它們會區分大小寫。  如果在名稱為 "LegacyId" 的使用者物件上設定延伸模組屬性，並在另一個名為 "LegacyId" 的使用者物件上設定，則當此屬性對應至使用名稱 "LegacyId" 的宣告時，將會成功抓取該資料，並在權杖中包含第一個使用者的宣告，而不是第二個。
>
> 在用於內建目錄屬性的宣告架構中，"Id" 參數是目錄延伸模組屬性的 "ExtensionID"。

## <a name="next-steps"></a>後續步驟
- 瞭解如何 [將自訂或其他宣告新增至 SAML 2.0 和 JSON Web 權杖， (JWT) 權杖](active-directory-optional-claims.md)。
- 瞭解如何 [針對特定應用程式自訂權杖中所發出的宣告](active-directory-claims-mapping.md)。