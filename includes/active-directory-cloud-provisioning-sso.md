---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504365"
---
## <a name="steps-to-enable-single-sign-on"></a>啟用單一登入的步驟
雲預配適用于單一登入。  當前，在安裝代理時沒有啟用 SSO 的選項，但您可以使用以下步驟啟用 SSO 並使用它。 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>第 1 步：下載並提取 Azure AD 連接檔
1.  首先，下載最新版本的[Azure AD 連接](https://www.microsoft.com/download/details.aspx?id=47594)
2.  使用管理許可權打開命令提示符，然後導航到剛剛下載的 msi。
3.  運行以下內容：`msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. 更改檔路徑和提取資料夾以匹配檔路徑和提取資料夾的名稱。  內容現在應位於提取資料夾中。

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>第 2 步：導入無縫 SSO 電源外殼模組

1. 下載並安裝[Azure AD 電源外殼](https://docs.microsoft.com/powershell/azure/active-directory/overview)。
2. 瀏覽至 `%programfiles%\Microsoft Azure Active Directory Connect` 資料夾。
3. 使用此命令匯入無縫 SSO PowerShell 模組：`Import-Module .\AzureADSSO.psd1`。

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>第 3 步：獲取已啟用無縫 SSO 的活動目錄林清單

1. 以系統管理員身分執行 PowerShell。 在 PowerShell 中，呼叫 `New-AzureADSSOAuthenticationContext`。 出現提示時，輸入您租用戶的全域管理員認證。
2. 呼叫 `Get-AzureADSSOStatus`。 此命令會提供已啟用這項功能的 Active Directory 樹系清單 (查看 [網域] 清單)。

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>步驟 4：為每個 Active Directory 樹系啟用無縫 SSO

1. 呼叫 `Enable-AzureADSSOForest`。 出現提示時，輸入預定 Active Directory 樹系的網域管理員認證。

   > [!NOTE]
   >域管理員憑據使用者名必須以 SAM 帳戶名稱格式（contoso_johndoe 或 contoso.com_johndoe）輸入。 我們使用使用者名的域部分來查找使用 DNS 的域管理員的網域控制站。

   >[!NOTE]
   >使用的域管理員帳戶不能是受保護使用者組的成員。 如果是這樣，操作將失敗。

2. 已設定此功能的每個 Active Directory 樹系，均重複上述步驟。

### <a name="step-5-enable-the-feature-on-your-tenant"></a>步驟 5。 啟用租用戶上的功能

若要在租用戶開啟此功能，請呼叫 `Enable-AzureADSSO -Enable $true`。
