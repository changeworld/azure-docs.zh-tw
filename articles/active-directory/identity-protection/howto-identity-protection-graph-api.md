---
title: Microsoft Graph PowerShell SDK 和 Azure Active Directory Identity Protection
description: 瞭解如何從 Azure Active Directory 查詢 Microsoft Graph 風險偵測和相關資訊
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2db8cfe652c0fca4b68b00d846e345c1b60cd05d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880231"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory Identity Protection 和 Microsoft Graph PowerShell SDK

Microsoft Graph 是 Microsoft 統一 API 端點，也是 [Azure Active Directory Identity Protection](./overview-identity-protection.md) API 的寄居地。 本文將說明如何使用 [Microsoft Graph POWERSHELL SDK](/graph/powershell/get-started) ，以使用 powershell 取得具風險的使用者詳細資料。 想要直接查詢 Microsoft Graph Api 的組織可以使用此文章 [：教學課程：使用 Microsoft Graph api 來識別及補救風險](/graph/tutorial-riskdetection-api) ，以開始該旅程。


## <a name="connect-to-microsoft-graph"></a>連接到 Microsoft Graph

透過 Microsoft Graph 存取 Identity Protection 資料需要四個步驟︰

- [建立憑證](#create-a-certificate)
- [建立新的應用程式註冊](#create-a-new-app-registration)
- [設定 API 許可權](#configure-api-permissions)
- [設定有效的認證](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>建立憑證

在生產環境中，您會使用來自生產憑證授權單位單位的憑證，但在此範例中，我們會使用自我簽署的憑證。 使用下列 PowerShell 命令來建立及匯出憑證。

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>建立新的應用程式註冊

1. 在 Azure 入口網站中，流覽至 **Azure Active Directory**  >  **應用程式註冊**。
1. 選取 [新增註冊]。
1. 在 [ **建立** ] 頁面上，執行下列步驟：
   1. 在 [ **名稱** ] 文字方塊中，輸入應用程式的名稱 (例如： Azure AD 風險偵測 API) 。
   1. 在 [ **支援的帳戶類型**] 下，選取將使用 api 的帳戶類型。
   1. 選取 [註冊]。
1. 記下 **應用程式 (用戶端) 識別碼** 和 **目錄 (租使用者) 識別碼** ，因為您稍後將需要這些專案。

### <a name="configure-api-permissions"></a>設定 API 許可權

在此範例中，我們會設定應用程式許可權，讓此範例可自動使用。 如果授與許可權給將登入的使用者，請改為選擇 [委派的許可權]。 如需不同許可權類型的詳細資訊，請參閱 Microsoft 身分識別平臺中的文章、 [許可權和同意](../develop/v2-permissions-and-consent.md#permission-types)。

1. 從您所建立的 **應用程式** 中，選取 [ **API 許可權**]。
1. 在 [ **設定的許可權** ] 頁面上，按一下頂端工具列中的 [ **新增許可權**]。
1. 在 [新增 API 存取權] 頁面中，按一下 [選取 API]。
1. 在 [選取 API] 頁面中，選取 [Microsoft Graph]，然後按一下 [選取]。
1. 在 [ **要求 API 許可權** ] 頁面上： 
   1. 選取 [應用程式權限]。
   1. 選取 [and] 旁的核取方塊 `IdentityRiskEvent.Read.All` `IdentityRiskyUser.Read.All` 。
   1. 選取 [新增權限]。
1. 選取 **[授與網域的管理員同意**] 

### <a name="configure-a-valid-credential"></a>設定有效的認證

1. 從您建立的 **應用程式** 中，選取 [ **憑證 & 秘密**]。
1. 在 [ **憑證**] 底下，選取 **[上傳憑證**]。
   1. 從開啟的視窗中選取先前匯出的憑證。
   1. 選取 [新增]。
1. 記下憑證 **指紋** ，因為下一個步驟中將會用到此資訊。

## <a name="list-risky-users-using-powershell"></a>使用 PowerShell 列出具風險的使用者

若要啟用查詢 Microsoft Graph 的功能，我們需要 `Microsoft.Graph` 使用命令在 PowerShell 視窗中安裝模組 `Install-Module Microsoft.Graph` 。

修改下列變數以包含先前步驟中產生的資訊，然後以整體方式執行這些變數，以使用 PowerShell 取得具風險的使用者詳細資料。

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>後續步驟

- [Microsoft Graph PowerShell SDK 入門](/graph/powershell/get-started)
- [教學課程：使用 Microsoft Graph Api 來識別和補救風險](/graph/tutorial-riskdetection-api)
- [Microsoft Graph 概觀](https://developer.microsoft.com/graph/docs)
- [無需使用者即進行存取](/graph/auth-v2-service)
- [Azure AD Identity Protection 服務根目錄](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
