---
title: 在 Azure 自動化中使用 Azure AD 對 Azure 進行驗證
description: 瞭解如何使用 Azure 自動化中的 Azure AD 做為提供者，以向 Azure 進行驗證。
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548330"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>在 Azure 自動化中使用 Azure AD 對 Azure 進行驗證

[Azure Active Directory （AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)服務可進行許多系統管理工作，例如使用者管理、網域管理和單一登入設定。 本文說明如何使用 Azure 自動化中的 Azure AD 做為提供者，以向 Azure 進行驗證。 

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="installing-azure-ad-modules"></a>安裝 Azure AD 模組

您可以透過下列 PowerShell 模組啟用 Azure AD：

* Azure Active Directory 適用于 Graph 的 PowerShell （AzureRM 和 Az 模組）。 Azure 自動化隨附于 AzureRM 模組及其最近的升級（Az 模組）。 功能包括使用 Azure AD 使用者（OrgId）以認證為基礎的驗證，對 Azure 進行非互動式驗證。 請參閱[Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76)。

* 適用于 Windows PowerShell 的 Microsoft Azure Active Directory （MSOnline 模組）。 此課程模組可讓您與 Microsoft Online 互動，包括 Office 365。

>[!NOTE]
>PowerShell Core 不支援 MSOnline 模組。 若要使用模組 Cmdlet，您必須從 Windows PowerShell 執行它們。 建議您將較新的 Azure Active Directory PowerShell 用於圖形模組，而不是使用 MSOnline 模組。 

### <a name="preinstallation"></a>事項

在您的電腦上安裝 Azure AD 模組之前：

* 卸載任何先前版本的 AzureRM/Az 模組和 MSOnline 模組。 

* 卸載 Microsoft Online Services 登入小幫手，以確保新 PowerShell 模組的運作正常。  

### <a name="install-the-azurerm-and-az-modules"></a>安裝 AzureRM 和 Az 模組

>[!NOTE]
>若要使用這些模組，您必須搭配64位版本的 Windows 使用 PowerShell 5.1 版或更新版本。 

1. 安裝 Windows Management Framework （WMF）5.1。 請參閱[安裝和設定 WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7)。

2. 使用在[Windows 上搭配 PowerShellGet 安裝 Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0)中的指示來安裝 AzureRM 和/或 Az。

### <a name="install-the-msonline-module"></a>安裝 MSOnline 模組

>[!NOTE]
>若要安裝 MSOnline 模組，您必須是 Office 365 系統管理員角色的成員。 請參閱[關於系統管理員角色](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)。

1. 確定已在您的電腦上啟用 Microsoft .NET Framework 3.5. x 功能。 您的電腦可能已安裝較新的版本，但可以啟用或停用與舊版 .NET Framework 的回溯相容性。 

2. 安裝64位版本的[Microsoft Online Services 登入](https://www.microsoft.com/download/details.aspx?id=41950)小幫手。

3. 以系統管理員身分執行 Windows PowerShell，以建立提升許可權的 Windows PowerShell 命令提示字元。

4. 從[MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0)部署 Azure Active Directory。

5. 如果系統提示您安裝 NuGet 提供者，請輸入 Y，然後按 ENTER 鍵。

6. 如果系統提示您從[PSGallery](https://www.powershellgallery.com/)安裝模組，請輸入 Y，然後按 enter 鍵。

### <a name="install-support-for-pscredential"></a>安裝 PSCredential 的支援

Azure 自動化使用[PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0)類別來代表認證資產。 您的`Get-AutomationPSCredential`腳本`PSCredential`會使用 Cmdlet 來抓取物件。 如需詳細資訊，請參閱[Azure 自動化中的認證資產](shared-resources/credentials.md)。

## <a name="assigning-a-subscription-administrator"></a>指派訂用帳戶管理員

您必須指派 Azure 訂用帳戶的系統管理員。 此人員擁有訂用帳戶範圍的擁有者角色。 請參閱[Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。 

## <a name="changing-the-azure-ad-users-password"></a>變更 Azure AD 使用者的密碼

若要變更 Azure AD 使用者的密碼：

1. 登出 Azure。

2. 讓系統管理員使用完整的使用者名稱（包括網域）和暫時密碼，以剛建立的 Azure AD 使用者身分登入 Azure。 

3. 要求系統管理員在出現提示時變更密碼。

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>設定 Azure 自動化以使用 Azure AD 使用者來管理 Azure 訂用帳戶

若要讓 Azure 自動化與 Azure AD 通訊，您必須將與 Azure 連線相關聯的認證抓取至 [Azure AD]。 這些認證的範例包括租使用者識別碼、訂用帳戶識別碼，以及 like。 如需有關 Azure 與 Azure AD 之間連線的詳細資訊，請參閱[將您的組織連線至 Azure Active Directory](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops)。

## <a name="creating-a-credential-asset"></a>建立認證資產

有了適用于 Azure AD 的 Azure 認證，就可以建立 Azure 自動化認證資產，以安全地儲存 Azure AD 認證，讓 runbook 和想要的狀態設定（DSC）腳本可以存取它們。 您可以使用 Azure 入口網站或 PowerShell Cmdlet 來執行這項操作。

### <a name="create-the-credential-asset-in-azure-portal"></a>在 Azure 入口網站中建立認證資產

您可以使用 Azure 入口網站來建立認證資產。 請使用**共用資源**下的**認證**，從您的自動化帳戶執行此作業。 請參閱[Azure 自動化中的認證資產](shared-resources/credentials.md)。

### <a name="create-the-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 建立認證資產

若要在 Windows PowerShell 中準備新的認證資產，您的腳本`PSCredential`會先使用指派的使用者名稱和密碼來建立物件。 接著，腳本會使用這個物件，透過呼叫[set-azureautomationcredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) Cmdlet 來建立資產。 或者，腳本可以呼叫[Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) Cmdlet 來提示使用者輸入名稱和密碼。 請參閱[Azure 自動化中的認證資產](shared-resources/credentials.md)。 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>從 Azure 自動化 runbook 管理 Azure 資源

您可以從使用認證資產的 Azure 自動化 runbook 來管理 Azure 資源。 以下是 PowerShell runbook 範例，它會收集用來停止和啟動 Azure 訂用帳戶中虛擬機器的認證資產。 此 runbook 會先`Get-AutomationPSCredential`使用來抓取要用來向 Azure 進行驗證的認證。 然後，它會呼叫[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) Cmdlet，以使用認證連線到 Azure。 腳本會使用[set-azuresubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) Cmdlet 來選擇要使用的訂用帳戶。 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>後續步驟

* 您可以[在 Azure 自動化中的認證資產](shared-resources/credentials.md)中找到自動化認證資產的相關資訊。
* 請參閱[管理 Azure 自動化中的模組](shared-resources/modules.md)，以瞭解如何使用自動化模組。
* 若要深入瞭解可在 Azure 自動化中用來啟動 runbook 的方法，請參閱[在 Azure 自動化中啟動 runbook](automation-starting-a-runbook.md)。
* 如需 PowerShell 的詳細資訊（包括語言參考和學習模組），請參閱[powershell](https://docs.microsoft.com/powershell/scripting/overview)檔。