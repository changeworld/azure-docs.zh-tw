---
title: Azure Active Directory Domain Services 疑難排解 |Microsoft Docs '
description: 瞭解如何對建立或管理 Azure Active Directory Domain Services 時常見的錯誤進行疑難排解
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 9593fe71fc4a29678d58d7c67699210a4a39f95e
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967370"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 的常見錯誤和疑難排解步驟

作為應用程式的身分識別和驗證的集中部分，Azure Active Directory Domain Services (Azure AD DS) 有時會發生問題。 如果您遇到問題，有一些常見的錯誤訊息和相關的疑難排解步驟，可協助您再次執行操作。 您也可以隨時 [開啟 Azure 支援要求][azure-support] ，以取得其他疑難排解協助。

本文提供 Azure AD DS 中常見問題的疑難排解步驟。

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>您無法為 Azure AD 目錄啟用 Azure AD 網域服務

如果您在啟用 Azure AD DS 時發生問題，請參閱下列常見錯誤和解決這些問題的步驟：

| **範例錯誤訊息** | **解決方法** |
| --- |:--- |
| *此網路上已使用名稱 aaddscontoso.com。請指定未使用的名稱。* |[虛擬網路中的網域名稱衝突](troubleshoot.md#domain-name-conflict) |
| *無法在此 Azure AD 租使用者中啟用網域服務。服務沒有足夠的許可權可使用名為「Azure AD Domain Services 同步」的應用程式。刪除名為「Azure AD Domain Services 同步處理」的應用程式，然後嘗試為您的 Azure AD 租使用者啟用網域服務。* |[網域服務沒有足夠的許可權可使用 Azure AD Domain Services 同步應用程式](troubleshoot.md#inadequate-permissions) |
| *無法在此 Azure AD 租使用者中啟用網域服務。您 Azure AD 租使用者中的網域服務應用程式沒有啟用網域服務的必要許可權。使用應用程式識別碼 d87dcbc6-a371-462e-88e3-28ad15ec4e64 來刪除應用程式，然後嘗試為您的 Azure AD 租使用者啟用網域服務。* |[未在您的 Azure AD 租使用者中正確設定網域服務應用程式](troubleshoot.md#invalid-configuration) |
| *無法在此 Azure AD 租使用者中啟用網域服務。Microsoft Azure AD 應用程式在您的 Azure AD 租使用者中已停用。使用應用程式識別碼 00000002-0000-0000-c000-000000000000 啟用應用程式，然後嘗試為您的 Azure AD 租使用者啟用網域服務。* |[您的 Azure AD 租用戶已停用 Microsoft Graph 應用程式](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>網域名稱衝突

**錯誤訊息**

*此網路上已使用名稱 aaddscontoso.com。請指定未使用的名稱。*

**解決方法**

確認您在相同或對等互連的虛擬網路上沒有現有的 AD DS 環境具有相同的功能變數名稱。 例如，您可能有一個名為 *aaddscontoso.com* 的 AD DS 網域在 Azure vm 上執行。 當您嘗試在虛擬網路上啟用具有相同 *aaddscontoso.com* 功能變數名稱的 Azure AD DS 受控網域時，要求的作業會失敗。

這項失敗是因為虛擬網路上功能變數名稱的名稱衝突。 DNS 查閱會檢查現有的 AD DS 環境是否回應所要求的功能變數名稱。 若要解決此錯誤，請使用不同的名稱來設定您的受控網域，或取消布建現有的 AD DS 網域，然後再試一次啟用 Azure AD DS。

### <a name="inadequate-permissions"></a>權限不足

**錯誤訊息**

*無法在此 Azure AD 租使用者中啟用網域服務。服務沒有足夠的許可權可使用名為「Azure AD Domain Services 同步」的應用程式。刪除名為「Azure AD Domain Services 同步處理」的應用程式，然後嘗試為您的 Azure AD 租使用者啟用網域服務。*

**解決方法**

檢查 Azure AD 目錄中是否有名為 *Azure AD Domain Services 同步* 處理的應用程式。 如果此應用程式存在，請將它刪除，然後再試一次啟用 Azure AD DS。 若要檢查現有的應用程式，並在需要時將其刪除，請完成下列步驟：

1. 在 Azure 入口網站中，從左側導覽功能表選取 [Azure Active Directory]。
1. 選取 [企業應用程式]。 從 [應用程式類型] 下拉式功能表選取 [所有應用程式]，然後選取 [套用]。
1. 在搜尋方塊中，輸入 *Azure AD Domain Services 同步*。如果應用程式存在，請選取它，然後選擇 [ **刪除**]。
1. 當您刪除應用程式之後，請再次嘗試啟用 Azure AD DS。

### <a name="invalid-configuration"></a>無效的組態

**錯誤訊息**

*無法在此 Azure AD 租使用者中啟用網域服務。您 Azure AD 租使用者中的網域服務應用程式沒有啟用網域服務的必要許可權。使用應用程式識別碼 d87dcbc6-a371-462e-88e3-28ad15ec4e64 來刪除應用程式，然後嘗試為您的 Azure AD 租使用者啟用網域服務。*

**解決方法**

檢查您的 Azure AD 目錄中是否有名為 *AzureActiveDirectoryDomainControllerServices* 的應用程式識別碼為 *d87dcbc6-a371-462e-88e3-28ad15ec4e64* 的現有應用程式。 如果此應用程式存在，請將它刪除，然後再試一次啟用 Azure AD DS。

使用下列 PowerShell 腳本來搜尋現有的應用程式實例，並視需要予以刪除：

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```

### <a name="microsoft-graph-disabled"></a>Microsoft Graph 已停用

**錯誤訊息**

*無法在此 Azure AD 租使用者中啟用網域服務。Microsoft Azure AD 應用程式在您的 Azure AD 租使用者中已停用。使用應用程式識別碼 00000002-0000-0000-c000-000000000000 啟用應用程式，然後嘗試為您的 Azure AD 租使用者啟用網域服務。*

**解決方法**

檢查您是否已停用識別碼為 *00000002-0000-0000-c000-000000000000*的應用程式。 此應用程式是 Microsoft Azure AD 應用程式，可提供 Azure AD 租用戶的圖形 API 存取權。 若要同步處理您的 Azure AD 租使用者，必須啟用此應用程式。

若要檢查此應用程式的狀態，並在需要時加以啟用，請完成下列步驟：

1. 在 Azure 入口網站中，從左側導覽功能表選取 [Azure Active Directory]。
1. 選取 [企業應用程式]。 從 [應用程式類型] 下拉式功能表選取 [所有應用程式]，然後選取 [套用]。
1. 在搜尋方塊中，輸入 *00000002-0000-0000-c000-00000000000*。 選取應用程式，然後選擇 [ **屬性**]。
1. 如果 [ **已啟用] 讓使用者登入** 設定為 [ *否*]，請將值設定為 *[是]*，然後選取 [ **儲存**]。
1. 啟用應用程式之後，請再次嘗試啟用 Azure AD DS。

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Azure Active Directory Domain Services 受控的網域

如果您 Azure AD 租使用者中的一或多個使用者無法登入受控網域，請完成下列疑難排解步驟：

* **認證格式** -請嘗試使用 UPN 格式來指定認證，例如 `dee@aaddscontoso.onmicrosoft.com` 。 UPN 格式是在 Azure AD DS 中指定認證的建議方式。 請確定已在 Azure AD 中正確設定此 UPN。

    如果您的租使用者中有多個使用者具有相同的 UPN 前置詞，或您的 UPN 前置詞太長，您帳戶的 *SAMAccountName* （例如 *AADDSCONTOSO\driley* ）可能會自動產生。 因此，您帳戶的 *SAMAccountName* 格式可能會與您在內部部署網域中預期或使用的格式不同。

* **密碼同步化**-請確定您已[使用 Azure AD Connect][hybrid-phs]為[僅限雲端的使用者][cloud-only-passwords]或混合式環境啟用密碼同步化。
    * **混合式同步處理的帳戶：** 如果受影響的使用者帳戶會從內部部署目錄同步處理，請確認下欄區域：
    
      * 您已部署或更新為 [最新建議的 Azure AD Connect 版本](https://www.microsoft.com/download/details.aspx?id=47594)。
      * 您已將 Azure AD Connect 設定為 [執行完整同步][hybrid-phs]處理。
      * 視您的目錄大小而定，可能需要一些時間，才能在受控網域中使用使用者帳戶和認證雜湊。 請務必等待足夠的時間，然後再嘗試對受控網域進行驗證。
      * 如果在驗證先前的步驟之後問題仍然存在，請嘗試重新開機 *Microsoft Azure AD 同步處理服務*。 從您的 Azure AD Connect 伺服器開啟命令提示字元，然後執行下列命令：
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **僅限雲端的帳戶**：如果受影響的使用者帳戶是僅限雲端的使用者帳戶，請在 [啟用 Azure AD DS 之後，確定使用者已變更其密碼][cloud-only-passwords]。 此密碼重設會導致產生受控網域所需的認證雜湊。

* **確認使用者帳戶為**作用中：根據預設，受控網域2分鐘內的五次無效密碼嘗試會導致使用者帳戶遭到鎖定30分鐘。 當帳戶被鎖定時，使用者無法登入。30分鐘之後，使用者帳戶會自動解除鎖定。
  * 受控網域上的密碼嘗試無效，無法在 Azure AD 中鎖定使用者帳戶。 使用者帳戶只會在受控網域內鎖定。 在 Active Directory 系統管理主控台中檢查使用者帳戶狀態 * (ADAC) * 使用 [管理 VM][management-vm]，而不是在 Azure AD 中。
  * 您也可以 [設定更細緻的密碼原則][password-policy] ，以變更預設的鎖定閾值和持續時間。

* **外部帳戶** ：檢查受影響的使用者帳戶不是 Azure AD 租使用者中的外部帳戶。 外部帳戶的範例包括 Microsoft 帳戶（例如， `dee@live.com` 或來自外部 Azure AD 目錄的使用者帳戶）。 Azure AD DS 不會儲存外部使用者帳戶的認證，因此無法登入受控網域。

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>受控網域有一或多個警示

如果受控網域上有作用中的警示，可能會導致驗證程式無法正常運作。

若要查看是否有任何作用中的警示，請 [檢查受控網域的健康情況狀態][check-health]。 如果顯示任何警示，請 [進行疑難排解並加以解決][troubleshoot-alerts]。

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>不會從受控網域中移除從 Azure AD 租用戶移除的使用者

Azure AD 防止意外刪除使用者物件。 當您從 Azure AD 的租使用者中刪除使用者帳戶時，對應的使用者物件會移至「回收站」。 當此刪除作業同步處理至您的受控網域時，對應的使用者帳戶會標示為停用。 這項功能可協助您復原或取消刪除使用者帳戶。

即使您在 Azure AD 目錄中使用相同的 UPN 重新建立使用者帳戶，使用者帳戶仍會在受控網域中保持為停用狀態。 若要從受控網域移除使用者帳戶，您必須從 Azure AD 租使用者強制刪除該帳戶。

若要從受控網域完全移除使用者帳戶，請使用 [Set-msoluser][Remove-MsolUser] PowerShell Cmdlet 搭配參數，從您的 Azure AD 租使用者中永久刪除使用者 `-RemoveFromRecycleBin` 。

## <a name="next-steps"></a>後續步驟

如果您持續遇到問題，請 [開啟 Azure 支援要求][azure-support] 以取得其他疑難排解協助。

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
