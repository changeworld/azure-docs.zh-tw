---
title: 非受控目錄的管理員接管-Azure AD |Microsoft Docs
description: 如何在非受控 Azure AD 組織 (影子租使用者) 中接管 DNS 功能變數名稱。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0472b2adb3213338b9fbc4e3a17a2c3444eb113
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647574"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>如何以系統管理員身分接管 Azure Active Directory 中非受控目錄

本文說明接管 Azure Active Directory (Azure AD) 之非受控目錄中 DNS 網域名稱的兩種方式。 當自助使用者註冊使用 Azure AD 的雲端服務時，系統會根據其電子郵件網域將其新增至非受控 Azure AD 目錄。 如需有關自助式或「病毒」註冊服務的詳細資訊，請參閱 [什麼是 Azure Active Directory 的自助式註冊？](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>決定要如何接管非受控目錄
在管理員接管的過程中，您可以依照[將自訂網域名稱新增到 Azure AD](../fundamentals/add-custom-domain.md) 中所述，證明擁有權。 下一節會更詳細地說明管理員體驗，但其摘要如下：

* 當您執行非受控 Azure 目錄的「內部」系統 [管理員接管](#internal-admin-takeover) 時，系統會將您新增為非受控目錄的全域管理員。 系統不會將任何使用者、網域或服務方案移轉至您所管理的其他目錄。

* 當您執行非受控 Azure 目錄的「外部」系統 [管理員接管](#external-admin-takeover) 時，您會將非受控目錄的 DNS 功能變數名稱新增至受控 azure 目錄。 當您新增網域名稱時，系統會在受控 Azure 目錄中建立使用者與資源的對應，以便讓使用者繼續存取服務而不中斷。 

## <a name="internal-admin-takeover"></a>內部管理員接管

某些包含 SharePoint 和 OneDrive 的產品（例如 Microsoft 365）不支援外部接管。 如果這是您的案例，或者您是系統管理員，而且想要接管使用自助式註冊的使用者所建立的非受控或「影子」 Azure AD 組織，您可以透過內部管理員接管來執行此作業。

1. 透過註冊 Power BI，在未受管理的組織中建立使用者內容。 為了便於範例說明，這些步驟會假設該路徑。

2. 開啟 [Power BI 網站](https://powerbi.com)，然後選取 [免費開始]。 輸入使用組織網域名稱的使用者帳戶，例如 `admin@fourthcoffee.xyz`。 輸入驗證碼之後，請查看您的電子郵件是否有確認碼。

3. 在來自 Power BI 的確認電子郵件中，選取 [是，這是我]。

4. 使用 Power BI 的使用者帳戶登入 [Microsoft 365 系統管理中心](https://portal.office.com/admintakeover) 。 您會收到一則訊息，指示您成為已在未受管理的組織中驗證之功能變數名稱的系統 **管理員** 。 請選取 [是，我想要成為管理員]。
  
   ![[成為管理員] 的第一個螢幕擷取畫面](./media/domains-admin-takeover/become-admin-first.png)
  
5. 在您的網域名稱登錄器新增 TXT 記錄，以證明您擁有網域名稱 **fourthcoffee.xyz**。 在此範例中為 GoDaddy.com。
  
   ![新增網域名稱的 txt 記錄](./media/domains-admin-takeover/become-admin-txt-record.png)

當您的網域註冊機構驗證 DNS TXT 記錄時，您可以管理 Azure AD 組織。

當您完成上述步驟時，您現在是 Microsoft 365 中第四個咖啡組織的全域管理員。 若要將功能變數名稱與其他 Azure 服務整合，您可以從 Microsoft 365 移除該名稱，並將其新增至 Azure 中的不同受控組織。

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>在 Azure AD 中將功能變數名稱新增至受管理的組織

1. 開啟 [Microsoft 365 系統管理中心](https://admin.microsoft.com)。
2. 選取 [ **使用者** ] 索引標籤，並使用不使用自訂功能變數名稱的名稱（例如 *使用者 \@ fourthcoffeexyz.onmicrosoft.com* ）建立新的使用者帳戶。 
3. 請確定新的使用者帳戶具有 Azure AD 組織的全域管理員許可權。
4. 開啟 Microsoft 365 系統管理中心中的 [ **網域** ] 索引標籤，選取功能變數名稱，然後選取 [ **移除**]。 
  
   ![從 Microsoft 365 移除功能變數名稱](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. 如果 Microsoft 365 中有任何使用者或群組參考移除的功能變數名稱，則必須將這些使用者或群組重新命名為 onmicrosoft.com 網域。 如果您強制刪除功能變數名稱，則會自動將所有使用者重新命名，在此範例中為 *使用者 \@ fourthcoffeexyz.onmicrosoft.com*。
  
6. 使用 Azure AD 組織的全域管理員帳戶登入 [Azure AD admin center](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 。
  
7. 選取 [自訂網域名稱]，然後新增網域名稱。 您將必須輸入 DNS TXT 記錄來驗證網域名稱擁有權。 
  
   ![網域已驗證為已新增至 Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> 在 Microsoft 365 組織中指派授權的 Power BI 或 Azure Rights Management 服務的任何使用者，都必須在移除功能變數名稱時儲存儀表板。 他們必須以使用者名稱（例如 *使用者 \@ fourthcoffeexyz.onmicrosoft.com* ）登入，而不是 *使用者 \@ fourthcoffee。*

## <a name="external-admin-takeover"></a>外部管理員接管

如果您已經使用 Azure 服務或 Microsoft 365 來管理組織，則無法新增自訂功能變數名稱（如果已在另一個 Azure AD 組織中進行驗證）。 不過，您可以從 Azure AD 中的受管理組織，以外部管理員接管的方式接管非受控組織。 一般程序會按照[將自訂網域名稱新增到 Azure AD](../fundamentals/add-custom-domain.md)一文所述。

當您確認功能變數名稱的擁有權時，Azure AD 會從未受管理的組織中移除功能變數名稱，並將其移至您現有的組織。 非受控目錄之外部管理員接管所需的 DNS TXT 驗證程序與內部管理員接管相同。 差異在於下列項目也會隨著網域名稱一起移過去：

- 使用者
- 訂用帳戶
- 授權指派

### <a name="support-for-external-admin-takeover"></a>對外部管理員接管的支援
以下是支援外部管理員接管的線上服務：

- Azure Rights Management
- Exchange Online

支援的服務方案包括：

- PowerApps Free
- PowerFlow Free
- 個人版 RMS
- Microsoft Stream
- Dynamics 365 免費試用版

具有包含 SharePoint、OneDrive 或商務用 Skype 之服務方案的任何服務，都不支援外部管理員接管;例如，透過 Office 免費訂用帳戶。 

您可以選擇性地使用 [ **ForceTakeover** 選項](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option)，將功能變數名稱從非受控組織中移除，並在所需的組織上進行驗證。 

#### <a name="more-information-about-rms-for-individuals"></a>個人版 RMS 的詳細資訊

針對 [個人版 RMS](/azure/information-protection/rms-for-individuals)，當非受控組織與您擁有的組織位於相同區域時，自動建立的 [Azure 資訊保護組織金鑰](/azure/information-protection/plan-implement-tenant-key) 和 [預設保護範本](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) 會另外隨功能變數名稱移動。

當未受管理的組織位於不同區域時，不會移動金鑰和範本。 例如，如果未受管理的組織位於歐洲，而您擁有的組織是北美洲。

雖然個人版 RMS 是針對支援 Azure AD 驗證來開啟受保護內容所設計，但它並不會阻止使用者同時保護內容。 如果使用者使用個人版 RMS 訂用帳戶來保護內容，且金鑰和範本未移動，則在網域接管之後就無法存取該內容。

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD 的 ForceTakeover 選項 PowerShell Cmdlet
您可以在 [PowerShell 範例](#powershell-example)中看到使用這些 Cmdlet。

Cmdlet | 使用方式
------- | -------
`connect-msolservice` | 出現提示時，請登入您的受控組織。
`get-msoldomain` | 顯示與目前組織相關聯的功能變數名稱。
`new-msoldomain –name <domainname>` | 將功能變數名稱新增至組織作為未驗證的 (尚未執行任何 DNS 驗證) 。
`get-msoldomain` | 功能變數名稱現在會包含在與您受控組織相關聯的功能變數名稱清單中，但會列為未 **驗證** 的名稱。
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | 提供要放到網域之新 DNS TXT 記錄中的資訊 (MS=xxxxx)。 驗證可能不會立即進行，因為 TXT 記錄需要一些時間傳播，所以請先稍候幾分鐘，再考慮使用 **-ForceTakeover** 選項。 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>如果您的網域名稱仍然未驗證，就可以著手執行 **-ForceTakeover** 選項。 它會驗證是否已建立 TXT 記錄，然後啟動接管程序。<li>只有在強制執行外部管理員接管時，例如，當非受控組織 Microsoft 365 服務封鎖接管時，才應該將 **-ForceTakeover** 選項新增至 Cmdlet。
`get-msoldomain` | 網域清單現在會將網域名稱顯示為 [已驗證]。

> [!NOTE]
> 當您執行外部接管強制選項之後，會在10天內刪除非受控 Azure AD 組織。

### <a name="powershell-example"></a>PowerShell 範例

1. 使用用來回應自助式供應項目的認證來連接至 Azure AD：
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. 取得網域清單：
  
   ```powershell
   Get-MsolDomain
   ```
3. 執行 Get-MsolDomainVerificationDns Cmdlet 以建立挑戰：
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    例如：
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. 複製從此命令傳回的值 (挑戰)。 例如：
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. 在公用 DNS 命名空間中，建立 DNS txt 記錄，其中包含您在上一個步驟中複製的值。 此記錄的名稱是父系網域的名稱，因此如果您使用 Windows Server 的 DNS 角色來建立此資源記錄，請將記錄名稱留白，而只要將此值貼到文字方塊中即可。
6. 執行 onfirm-MsolDomain Cmdlet 來驗證挑戰：
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   例如：
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

成功挑戰會讓您回到提示，但不會產生錯誤。

## <a name="next-steps"></a>後續步驟

* [將自訂網域名稱新增到 Azure AD](../fundamentals/add-custom-domain.md)
* [如何安裝和設定 Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure Cmdlet 參考](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0&preserve-view=true)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
