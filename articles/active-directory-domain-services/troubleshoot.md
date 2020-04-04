---
title: Azure 活動目錄域服務故障排除 |微軟文件
description: 瞭解如何建立或管理 Azure 的目錄網域服務時解決常見錯誤
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 84efe294533186fdcf2e0a3356a7d6b01eccaf5f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654401"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Azure 活動目錄域服務的常見錯誤和故障排除步驟

作為應用程式識別和身份驗證的核心部分,Azure 活動目錄域服務 (Azure AD DS) 有時存在問題。 如果遇到問題,則有一些常見的錯誤消息和相關故障排除步驟,以説明您重新運行某些操作。 您可以隨時[打開 Azure 支援請求][azure-support],以獲取其他故障排除説明。

本文提供了 Azure AD DS 中常見問題的故障排除步驟。

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>您無法為 Azure AD 目錄啟用 Azure AD 網域服務

如果在啟用 Azure AD DS 時遇到問題,請查看以下常見錯誤和解決這些問題的步驟:

| **範例錯誤訊息** | **解析度** |
| --- |:--- |
| *此網路上已使用aaddscontoso.com的名稱。指定不使用的名稱。* |[虛擬網路中的網域名稱衝突](troubleshoot.md#domain-name-conflict) |
| *無法在此 Azure AD 租戶中啟用域服務。該服務對稱為「Azure AD 域服務同步」的應用程式沒有適當的許可權。刪除名為"Azure AD 域服務同步"的應用程式,然後嘗試為 Azure AD 租戶啟用域服務。* |[網域服務對 Azure AD 網域服務同步應用程式沒有足夠的權限](troubleshoot.md#inadequate-permissions) |
| *無法在此 Azure AD 租戶中啟用域服務。Azure AD 租戶中的域服務應用程式沒有啟用域服務所需的許可權。使用應用程式識別符 d87dcbc6-a371-462e-88e3-28ad15ec4e64 刪除應用程式,然後嘗試為 Azure AD 租戶啟用域服務。* |[網域服務應用程式未在 Azure AD 租戶中正確配置](troubleshoot.md#invalid-configuration) |
| *無法在此 Azure AD 租戶中啟用域服務。在 Azure AD 租戶中禁用 Microsoft Azure AD 應用程式。使用應用程式識別碼 0000000-0000-0000-c000-0000000000啟用應用程式,然後嘗試為Azure AD租戶啟用功能服務。* |[您的 Azure AD 租用戶已停用 Microsoft Graph 應用程式](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>網域名稱衝突

**錯誤訊息**

*此網路上已使用aaddscontoso.com的名稱。指定不使用的名稱。*

**解析度**

檢查您沒有具有相同網功能變數名稱的現有 ADDS 環境,或者對等虛擬網路。 例如,您可能有一個名為*aaddscontoso.com*的 ADDS 域在 Azure VM 上運行。 當您嘗試在虛擬網路上啟用具有相同*aaddscontoso.com*網域名稱的 Azure AD DS 託管域時,請求的操作將失敗。

此失敗是由於虛擬網路上的功能變數名稱的名稱衝突造成的。 DNS 查找檢查現有 AD DS 環境是否回應請求的功能變數名稱。 要解決此故障,請使用其他名稱設置 Azure AD DS 託管域,或取消預配現有的 AD DS 域,然後重試啟用 Azure AD DS。

### <a name="inadequate-permissions"></a>權限不足

**錯誤訊息**

*無法在此 Azure AD 租戶中啟用域服務。該服務對稱為「Azure AD 域服務同步」的應用程式沒有適當的許可權。刪除名為"Azure AD 域服務同步"的應用程式,然後嘗試為 Azure AD 租戶啟用域服務。*

**解析度**

檢查 Azure AD 目錄中是否有名為*Azure AD 網域服務同步*的應用程式。 如果此應用程式存在,請將其刪除,然後重試以啟用 Azure AD DS。 要檢查現有應用程式並在需要時將其刪除,請完成以下步驟:

1. 在 Azure 門戶中,從左邊導航選單中選擇**Azure 的活動目錄**。
1. 選取 [企業應用程式]****。 從 **「應用程式類型」** 下拉選單中選擇*所有應用程式*,然後選擇 **"應用**"。
1. 在搜尋框中,輸入*Azure AD 網域服務同步*。如果應用程式存在,請選擇它並選擇 **「刪除**」。。
1. 刪除應用程式後,請嘗試再次啟用 Azure AD DS。

### <a name="invalid-configuration"></a>無效的組態

**錯誤訊息**

*無法在此 Azure AD 租戶中啟用域服務。Azure AD 租戶中的域服務應用程式沒有啟用域服務所需的許可權。使用應用程式識別符 d87dcbc6-a371-462e-88e3-28ad15ec4e64 刪除應用程式,然後嘗試為 Azure AD 租戶啟用域服務。*

**解析度**

檢查 Azure AD 目錄中是否有名為*AzureActiveDirectory DomainController 服務*的現有應用程式,應用程式識別碼*為 d87dcbc6-a371-462e-88e3-28ad15ec4e64。* 如果此應用程式存在,請將其刪除,然後重試以啟用 Azure AD DS。

使用以下 PowerShell 文稿搜尋現有應用程式實體,並在需要時將其刪除:

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

*無法在此 Azure AD 租戶中啟用域服務。在 Azure AD 租戶中禁用 Microsoft Azure AD 應用程式。使用應用程式識別碼 0000000-0000-0000-c000-0000000000啟用應用程式,然後嘗試為Azure AD租戶啟用功能服務。*

**解析度**

檢查是否禁用了具有標識符 0000000-0000-c000-0000000000000000000000000000000000000000000000000 0000000000000000000000000000000000000000000000000000000000000000000000000 *00000002-0000-0000-c000-000000000000* 此應用程式是 Microsoft Azure AD 應用程式，可提供 Azure AD 租用戶的圖形 API 存取權。 要同步 Azure AD 租戶,必須啟用此應用程式。

要檢查此應用程式的狀態並在需要時啟用它,請完成以下步驟:

1. 在 Azure 門戶中,從左邊導航選單中選擇**Azure 的活動目錄**。
1. 選取 [企業應用程式]****。 從 **「應用程式類型」** 下拉選單中選擇*所有應用程式*,然後選擇 **"應用**"。
1. 在搜索框中,輸入*0000000-0000-0000-c000-0000000000000000000000。* 選擇應用程式,然後選擇**屬性**。
1. 如果**啟用使用者登錄**設定為 *"否*",則將值設置為 *"是*",則選擇"**儲存**"。
1. 啟用應用程式後,請嘗試再次啟用 Azure AD DS。

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Azure Active Directory Domain Services 受控的網域

如果 Azure AD 租戶中的一個或多個使用者無法登入 Azure AD DS 託管域,請完成以下故障排除步驟:

* **認證格式**- 嘗試使用 UPN 格式指定`dee@aaddscontoso.onmicrosoft.com`認證, 如 。 UPN 格式是在 Azure AD DS 中指定憑據的推薦方式。 確保此 UPN 在 Azure AD 中設定正確。

    如果租戶中有多個具有相同 UPN 首碼的使用者,或者您的 UPN 前綴過長,則自動生成帳戶*名稱*,例如*AADDSCONTOSO_driley。* 因此,您帳戶的*SAMAccountName*格式可能與您期望或在本地域中使用的內容不同。

* **密碼同步**- 確保已為[僅雲端使用者][cloud-only-passwords]或使用 Azure AD Connect 為[混合環境][hybrid-phs]啟用密碼同步。
    * **混合同步帳戶:** 如果受影響的使用者帳戶從本地目錄同步,請驗證以下區域:
    
      * 您已部署或更新到[Azure AD 連線的最新推薦版本](https://www.microsoft.com/download/details.aspx?id=47594)。
      * 已設定 Azure AD 連線以[執行完整同步][hybrid-phs]。
      * 根據目錄的大小,使用者帳戶和憑據哈希可能需要一段時間才能在 Azure AD DS 中可用。 在嘗試對託管域進行身份驗證之前,請確保等待足夠長的時間。
      * 如果在驗證前面的步驟後問題仍然存在,請嘗試重新啟動*Microsoft Azure AD 同步服務*。 從 Azure AD 連線伺服器開啟指令提示並執行以下指令:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **只限雲帳戶**:如果受影響的使用者帳戶是僅雲端使用者帳戶,請確保[使用者在啟用 Azure AD DS 後更改了密碼][cloud-only-passwords]。 此密碼重置會導致生成 Azure AD 網域服務所需的認證哈希。

* **驗證使用者帳戶處於活動狀態**:默認情況下,在託管域上 2 分鐘內的 5 次無效密碼嘗試會導致使用者帳戶被鎖定 30 分鐘。 當帳戶被鎖定時,使用者無法登錄。30 分鐘后,使用者帳戶將自動解鎖。
  * Azure AD DS 託管域上的無效密碼嘗試不會鎖定 Azure AD 中的使用者帳戶。 使用者帳戶僅在託管域中鎖定。 使用[管理 VM(][management-vm]不在 Azure AD*中)檢查活動目錄管理主控台 (ADAC)* 中的使用者帳戶狀態。
  * 您還可以[配置細粒度密碼策略][password-policy]以更改默認鎖定閾值和持續時間。

* **外部帳號**- 檢查受影響的使用者帳戶不是 Azure AD 租戶中的外部帳戶。 外部帳戶的範例包括 Microsoft 帳戶`dee@live.com`(如)或來自外部 Azure AD 目錄的使用者帳戶。 Azure AD DS 不儲存外部使用者帳戶的認證,因此無法登錄到託管域。

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>受控網域有一或多個警示

如果 Azure AD DS 託管域上有活動警報,則可能會阻止身份驗證過程正常工作。

要檢視是否有任何活動警報,[請檢查 Azure AD DS 託管域的運行狀況][check-health]狀態。 如果顯示任何警報,[則排除故障並解決它們][troubleshoot-alerts]。

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>不會從受控網域中移除從 Azure AD 租用戶移除的使用者

Azure AD 可防止意外刪除使用者物件。 從 Azure AD 租戶中刪除使用者帳戶時,相應的使用者物件將移動到回收站。 當此刪除操作同步到 Azure AD DS 託管域時,相應的使用者帳戶將標記為已禁用。 此功能可幫助您恢復或取消刪除使用者帳戶。

使用者帳戶在 Azure AD DS 託管域中保持禁用狀態,即使您在 Azure AD 目錄中重新創建具有相同 UPN 的使用者帳戶也是如此。 要從 Azure AD DS 託管域中刪除使用者帳戶,需要強制將其從 Azure AD 租戶中刪除。

要從 Azure AD DS 託管域中完全刪除使用者帳戶,請使用`-RemoveFromRecycleBin`帶有 該參數[的「刪除-MsolUser][Remove-MsolUser] PowerShell cmdlet」永久刪除 Azure AD 租戶中的使用者帳戶。

## <a name="next-steps"></a>後續步驟

如果仍然存在問題,[請打開 Azure 支援請求][azure-support]以取得其他故障排除説明。

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
