---
title: 在宣告中使用 Azure AD 架構擴充屬性
titleSuffix: Microsoft identity platform
description: 描述如何使用目錄架構擴充屬性，將使用者資料傳送至權杖宣告中的應用程式。
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
ms.openlocfilehash: 4450b0bcc06b048fd9ad42d2a7bf1c588816eae7
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115606"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>在宣告中使用目錄架構延伸屬性

目錄架構擴充屬性提供一種方式，可將其他資料儲存在使用者物件和其他目錄物件（例如群組、租使用者詳細資料、服務主體）的 Azure Active Directory 中。  只有使用者物件的擴充屬性可以用來發出宣告給應用程式。 本文說明如何使用目錄架構擴充屬性，將使用者資料傳送至權杖宣告中的應用程式。

> [!NOTE]
> Microsoft Graph 提供兩個其他的擴充機制來自訂繪圖物件。 這些稱為 Microsoft Graph 開放式擴充功能和 Microsoft Graph 架構延伸模組。 如需詳細資訊，請參閱[Microsoft Graph 檔](/graph/extensibility-overview)。 儲存在使用這些功能之 Microsoft Graph 物件上的資料，無法做為權杖中宣告的來源。

目錄架構擴充屬性一律會與租使用者中的應用程式相關聯，且應用程式的*applicationId*會在其名稱中參考它們。

目錄架構延伸模組屬性的識別碼的格式為*Extension_xxxxxxxxx_AttributeName*。  其中， *xxxxxxxxx*是為其定義副檔名的應用程式的*applicationId* 。

## <a name="registering-and-using-directory-schema-extensions"></a>註冊和使用目錄架構延伸模組
目錄架構擴充屬性可以透過下列兩種方式的其中一種來註冊和填入：

- 藉由設定 AD Connect 來建立它們，並將資料從內部部署 AD 同步處理到其中。 請參閱[Azure AD Connect 同步目錄延伸](../hybrid/how-to-connect-sync-feature-directory-extensions.md)模組。
- 使用 Microsoft Graph 來註冊、設定的值，並從目錄架構延伸模組屬性讀取[目錄架構擴充功能 |圖形 API 概念](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions)及/或 powershell +[使用 AzureAD Powershell Cmdlet 管理延伸模組屬性](/powershell/azure/active-directory/using-extension-attributes-sample?view=azureadps-2.0)。

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>以 AD Connect 所建立之目錄架構延伸屬性的資料發出宣告
使用 AD Connect 建立和同步的目錄架構擴充屬性一律會與 AD Connect 所使用的應用程式識別碼相關聯。 它們可以做為宣告的來源，方法是在入口網站 UI 中的 [**企業應用**程式] 設定中，將這些應用程式設定為使用資源庫或非資源庫**應用程式設定體驗，並**透過應用程式註冊體驗註冊的應用程式的宣告對應原則來進行。  一旦透過 AD Connect 建立的目錄延伸模組屬性位於目錄中，它就會顯示在 [SAML SSO 宣告設定] UI 中。

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>發出宣告，其中包含使用 Graph 或 PowerShell 為應用程式建立的目錄架構延伸屬性中的資料
如果目錄架構延伸模組屬性是使用 Microsoft Graph 或 PowerShell (透過應用程式的初始安裝或布建步驟) 來註冊應用程式，則可以在 Azure Active Directory 中設定相同的應用程式，以便在使用者登入時從宣告中的使用者物件接收該屬性中的資料。  應用程式可以設定為接收目錄架構延伸中的資料，這些會使用[選擇性宣告](active-directory-optional-claims.md#configuring-directory-extension-optional-claims)在該相同應用程式上註冊。  這些可以在應用程式資訊清單中設定。  這可讓多租使用者應用程式註冊目錄架構擴充屬性，供自己使用。 將應用程式布建到租使用者時，會將相關聯的目錄架構延伸模組設為可在該租使用者中的使用者上進行設定，並加以使用。  一旦在租使用者中設定並同意，它就可以用來透過圖表儲存和抓取資料，並對應至 Microsoft 身分識別平臺發出給應用程式的權杖中的宣告。

您可以為任何應用程式註冊並填入目錄架構擴充屬性。

如果應用程式需要從在不同應用程式上註冊的延伸模組屬性中傳送資料的宣告，則必須使用[宣告對應原則](active-directory-claims-mapping.md)，將延伸模組屬性對應至宣告。  管理目錄架構延伸模組屬性的常見模式是建立應用程式，特別是您所需的所有架構延伸模組的註冊點。  它不必是真正的應用程式，而且這項技術表示所有延伸模組的名稱都有相同的應用程式識別碼。

例如，以下是宣告對應原則，可從 OAuth/OIDC 權杖中的目錄架構延伸屬性發出單一宣告：

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

其中*xxxxxxx*是用來註冊延伸模組的應用程式識別碼。

> [!TIP]
> 在物件上設定目錄擴充屬性時，大小寫一致性相當重要。  設定時，延伸模組屬性名稱不區分大小寫，但是權杖服務從目錄讀取時，它們會區分大小寫。  如果在名為 "LegacyId" 的使用者物件上，以及在另一個名稱為 "LegacyId" 的使用者物件上設定了擴充屬性，則當此屬性對應至使用名稱 "LegacyId" 的宣告時，將會成功抓取資料，而且第一個使用者的權杖中會包含宣告，而不是第一個使用者。
>
> 在用於內建目錄屬性的宣告架構中，"Id" 參數是目錄擴充屬性的 "ExtensionID"。

## <a name="next-steps"></a>後續步驟
- 瞭解如何[將自訂或額外的宣告新增至 SAML 2.0 和 JSON Web 權杖 (JWT) 權杖](active-directory-optional-claims.md)。 
- 瞭解如何[自訂特定應用程式的權杖中所發出的宣告](active-directory-claims-mapping.md)。