---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "79504365"
---
## <a name="steps-to-enable-single-sign-on"></a>啟用單一登入的步驟
雲端布建可搭配單一登入使用。  目前沒有在安裝代理程式時啟用 SSO 的選項，不過您可以使用下列步驟來啟用 SSO 並使用它。 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>步驟1：下載並解壓縮 Azure AD Connect 檔案
1.  首先，請下載最新版本的 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  使用系統管理許可權開啟命令提示字元，然後流覽至您剛剛下載的 msi。
3.  執行下列動作：  `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. 變更 filepath 和 extractfolder，以符合您的檔案路徑和您的解壓縮資料夾名稱。  內容現在應該會在 [解壓縮] 資料夾中。

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>步驟2：匯入無縫 SSO PowerShell 模組

1. 下載並安裝 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)。
2. 瀏覽至 `%programfiles%\Microsoft Azure Active Directory Connect` 資料夾。
3. 使用此命令匯入無縫 SSO PowerShell 模組：`Import-Module .\AzureADSSO.psd1`。

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>步驟3：取得已啟用無縫 SSO 的 Active Directory 樹系列表

1. 以系統管理員身分執行 PowerShell。 在 PowerShell 中，呼叫 `New-AzureADSSOAuthenticationContext`。 出現提示時，輸入您租用戶的全域管理員認證。
2. 呼叫 `Get-AzureADSSOStatus`。 此命令會提供已啟用這項功能的 Active Directory 樹系清單 (查看 [網域] 清單)。

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>步驟 4：為每個 Active Directory 樹系啟用無縫 SSO

1. 呼叫 `Enable-AzureADSSOForest`。 出現提示時，輸入預定 Active Directory 樹系的網域管理員認證。

   > [!NOTE]
   >必須以 SAM 帳戶名稱格式 (contoso\johndoe 或 contoso.com\johndoe) 輸入網域系統管理員認證使用者名稱。 我們會利用使用者名稱的網域部分，使用 DNS 來尋找網域系統管理員的網域控制站。

   >[!NOTE]
   >使用的網域系統管理員帳戶不得為「受保護的使用者」群組的成員。 若是如此，作業會失敗。

2. 已設定此功能的每個 Active Directory 樹系，均重複上述步驟。

### <a name="step-5-enable-the-feature-on-your-tenant"></a>步驟 5。 啟用租用戶上的功能

若要在租用戶開啟此功能，請呼叫 `Enable-AzureADSSO -Enable $true`。
