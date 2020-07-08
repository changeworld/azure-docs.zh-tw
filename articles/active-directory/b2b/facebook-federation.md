---
title: 將 Facebook 新增為識別提供者 - Azure AD
description: 與 Facebook 同盟，以允許外部使用者 (來賓) 使用自己的 Facebook 帳戶登入您的 Azure AD 應用程式。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b5e1db2c86f6118c3cd333974c9cfd64f747128
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85551438"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>將 Facebook 新增為外部身分識別的識別提供者

您可以將 Facebook 新增至您的自助式註冊使用者流程 (預覽)，讓使用者可以使用自己的 Facebook 帳戶來登入您的應用程式。 若要讓使用者能夠使用 Facebook 登入，您必須先[為您的租用戶啟用自助式註冊](self-service-sign-up-user-flow.md)。 將 Facebook 新增為身分識別提供者之後，請設定應用程式的使用者流程，然後選取 Facebook 作為其中一個登入選項。

> [!NOTE]
> 使用者只能使用其 Facebook 帳戶來透過應用程式使用自助式註冊和使用者流程進行註冊。 使用者無法使用 Facebook 帳戶接受邀請及兌換其邀請。

## <a name="create-an-app-in-the-facebook-developers-console"></a>在 Facebook 開發人員主控台中建立應用程式

若要使用 Facebook 帳戶作為[識別提供者](identity-providers.md)，您必須在 Facebook 開發人員主控台中建立應用程式。 如果您還沒有 Facebook 帳戶，可以在 [https://www.facebook.com/](https://www.facebook.com) 註冊。

> [!NOTE]  
> 在下面的步驟 9 和 16 中使用下列 URL。
> - 在 [**網站 URL** ] 中輸入應用程式的位址，例如 `https://contoso.com` 。
> - 在 [有效的 OAuth 重新導向 URI] 中，輸入 `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp`。 您可以在 [Azure Active Directory 概觀] 分頁中找到您的 `<tenant-ID>`。


1. 請以您的 Facebook 帳戶認證登入 [Facebook for developers (開發人員專用的 Facebook)](https://developers.facebook.com/)。
2. 如果您尚未這麼做，您需要註冊為 Facebook 開發人員。 若要這樣做，請選取頁面右上角的 [開始]，接受 Facebook 的原則，然後完成註冊步驟。
3. 選取 [我的應用程式]，然後選取 [建立應用程式]。
4. 輸入**顯示名稱**和有效的**連絡人電子郵件**。
5. 選取 [建立應用程式識別碼]。 這可能會要求您接受 Facebook 平台原則，並完成線上安全性檢查。
6. 選取 [設定] > [基本]。
7. 選擇**類別**，例如 [商務] 和 [頁面]。 此為 Facebook 所需的值，但不會用於 Azure AD。
8. 在頁面底部選取 [新增平台]，然後選取 [網站]。
9. 在 [網站 URL] 中，輸入適當的 URL (如上所述)。
10. 在 [隱私權原則 URL] 中，輸入您用來維護應用程式隱私權資訊的頁面 URL，例如 `http://www.contoso.com`。
11. 選取 [Save Changes] \(儲存變更\)。
12. 在頁面頂端複製 [應用程式識別碼] 的值。
13. 選取 [顯示]，並複製 [應用程式密碼] 的值。 您必須同時使用這兩個值，將 Facebook 設定為租用戶中的身分識別提供者。 **應用程式密碼**是重要的安全性認證。
14. 選取 [產品] 旁的加號，然後選取 [Facebook 登入] 底下的 [設定]。
15. 在 [Facebook 登入] 下方選取 [設定]。
16. 在 [有效的 OAuth 重新導向 URI] 中，輸入適當的 URL (如上所述)。
17. 選取頁面底部的 [儲存變更]。
18. 若要讓 Azure AD 能使用您的 Facebook 應用程式，請選取頁面右上方的 [狀態選取器]，然後將其切換至 [開啟]，以將應用程式設為公用，然後選取 [切換模式]。 此時，狀態應該會從 [開發] 變更為 [作用中]。
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>將 Facebook 帳戶設為識別提供者
現在，您會設定 Facebook 用戶端識別碼和用戶端密碼，方法是在 Azure AD 入口網站中加以輸入，或是使用 PowerShell 來輸入。 您可以在已啟用自助式註冊的應用程式上透過使用者流程進行註冊，來測試您的 Facebook 設定。

### <a name="to-configure-facebook-federation-in-the-azure-ad-portal"></a>在 Azure AD 入口網站中設定 Facebook 同盟
1. 以 Azure AD 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [Azure 服務] 底下，選取 [Azure Active Directory]。
3. 在左側功能表中，選取 [外部身分識別]。
4. 選取 [所有識別提供者]，然後選取 [Facebook]。
5. 在 [用戶端識別碼] 中，輸入稍早所建立 Facebook 應用程式的**應用程式識別碼**。
6. 在 [用戶端祕密] 中，輸入您記下的**應用程式祕密**。

   ![顯示 [新增社交識別提供者] 頁面的螢幕擷取畫面](media/facebook-federation/add-social-identity-provider-page.png)

7. 選取 [儲存]。
### <a name="to-configure-facebook-federation-by-using-powershell"></a>使用 PowerShell 設定 Facebook 同盟
1. 安裝最新版的 Azure AD PowerShell for Graph 模組 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 執行下列命令：`Connect-AzureAD`。
3. 在出現登入提示時，使用受控的全域系統管理員帳戶來登入。  
4. 執行以下命令： 
   
   `New-AzureADMSIdentityProvider -Type Facebook -Name Facebook -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > 使用您於上方在 Facebook 開發人員主控台所建立之應用程式中的用戶端識別碼和用戶端密碼。 如需詳細資訊，請參閱 [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) 一文。 

## <a name="how-do-i-remove-facebook-federation"></a>要如何移除 Facebook 同盟？
您可以刪除 Facebook 同盟設定。 如果您這麼做，使用其 Facebook 帳戶透過使用者流程註冊的所有使用者都將無法登入。 

### <a name="to-delete-facebook-federation-in-the-azure-ad-portal"></a>在 Azure AD 入口網站中刪除 Facebook 同盟： 
1. 移至 [Azure 入口網站](https://portal.azure.com)。 在左窗格中，選取 [Azure Active Directory]。 
2. 選取 [外部身分識別]。
3. 選取 [所有識別提供者]。
4. 在 [Facebook] 行上，選取操作功能表 ( **...** )，然後選取 [刪除]。 
5. 選取 [是] 以確認要刪除。

### <a name="to-delete-facebook-federation-by-using-powershell"></a>使用 PowerShell 刪除 Facebook 同盟： 
1. 安裝最新版的 Azure AD PowerShell for Graph 模組 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 執行 `Connect-AzureAD`。  
4. 在出現登入提示時，使用受控的全域管理員帳戶來登入。  
5. 輸入下列命令：

    `Remove-AzureADMSIdentityProvider -Id Facebook-OAUTH`

   > [!NOTE]
   > 如需詳細資訊，請參閱 [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)。 

## <a name="next-steps"></a>後續步驟

- [新增應用程式的自助式註冊](self-service-sign-up-user-flow.md)
