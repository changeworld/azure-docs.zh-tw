---
title: 在 Azure 自動化中使用 Azure AD 對 Azure 進行驗證
description: 此文章說明如何在 Azure 自動化中，使用 Azure AD 作為提供者向 Azure 進行驗證。
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: bbd1f977b548c2b8e7980709ea125c07e22b1fa2
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400667"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>使用 Azure AD 向 Azure 進行驗證

[Azure Active Directory (AD)](../active-directory/fundamentals/active-directory-whatis.md) 服務可啟用許多系統管理工作，例如使用者管理、網域管理和單一登入設定。 此文章描述如何在 Azure 自動化中，使用 Azure AD 作為提供者向 Azure 進行驗證。 

## <a name="install-azure-ad-modules"></a>安裝 Azure AD 模組

您可以透過下列 PowerShell 模組啟用 Azure AD：

* 適用於 Graph (AzureRM 和 Az 模組) 的 Azure Active Directory PowerShell。 Azure 自動化隨附 AzureRM 模組及其最近的升級 (Az 模組)。 功能包括使用 Azure AD 使用者 (OrgId) 認證型驗證，對 Azure 進行非互動式驗證。 請參閱 [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76) \(英文\)。

* 適用於 Windows PowerShell 的 Microsoft Azure Active Directory (MSOnline 模組)。 此課程模組可讓您與 Microsoft Online 互動，包括 Microsoft 365。

>[!NOTE]
>PowerShell Core 不支援 MSOnline 模組。 若要使用模組 Cmdlet，您必須從 Windows PowerShell 加以執行。 建議您使用較新的適用於 Graph 的 Azure Active Directory PowerShell 模組，而不是使用 MSOnline 模組。 

### <a name="preinstallation"></a>預先安裝

在您的電腦上安裝 Azure AD 模組之前：

* 將任何先前版本的 AzureRM/Az 模組和 MSOnline 模組解除安裝。 

* 將 Microsoft Online Services 登入小幫手解除安裝，以確保新 PowerShell 模組的運作正常。  

### <a name="install-the-azurerm-and-az-modules"></a>安裝 AzureRM 和 Az 模組

>[!NOTE]
>若要使用這些模組，您必須搭配 64 位元版本的 Windows 使用 PowerShell 5.1 版或更新版本。 

1. 安裝 Windows Management Framework (WMF) 5.1。 請參閱[安裝與設定 WMF 5.1](/powershell/scripting/wmf/setup/install-configure?view=powershell-7)。

2. 使用[使用 PowerShellGet 在 Windows 上安裝 Azure PowerShell ](/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0)中的指示，安裝 AzureRM 和/或 Az。

### <a name="install-the-msonline-module"></a>安裝 MSOnline 模組

>[!NOTE]
>若要安裝 MSOnline 模組，您必須是系統管理員角色的成員。 請參閱[關於系統管理員角色](/microsoft-365/admin/add-users/about-admin-roles)。

1. 確定已在您的電腦上啟用 Microsoft .NET Framework 3.5.x 功能。 您的電腦可能已安裝較新的版本，但可以啟用或停用與舊版 .NET Framework 的回溯相容性。 

2. 安裝 64 位元版本的 [Microsoft Online Services 登入小幫手](https://www.microsoft.com/download/details.aspx?id=41950)。

3. 以系統管理員身分執行 Windows PowerShell，以建立提升權限的 Windows PowerShell 命令提示字元。

4. 從 [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0) \(英文\) 部署 Azure Active Directory。

5. 如果系統提示您安裝 NuGet 提供者，請輸入 Y，然後按 ENTER。

6. 如果系統提示您從 [PSGallery](https://www.powershellgallery.com/) \(英文\) 安裝模組，請輸入 Y，然後按 ENTER。

### <a name="install-support-for-pscredential"></a>安裝 PSCredential 的支援

Azure 自動化使用 [PSCredential](/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) 類別來代表認證資產。 您的指令碼會使用 `Get-AutomationPSCredential` Cmdlet 來擷取 `PSCredential` 物件。 如需詳細資訊，請參閱 [Azure 自動化中的認證資產](shared-resources/credentials.md)。

## <a name="assign-a-subscription-administrator"></a>指派訂用帳戶系統管理員

您必須指派 Azure 訂用帳戶的系統管理員。 此人員有訂用帳戶範圍的擁有者角色。 請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。 

## <a name="change-the-azure-ad-users-password"></a>變更 Azure AD 使用者的密碼

變更 Azure AD 使用者的密碼：

1. 登出 Azure。

2. 讓系統管理員使用完整的使用者名稱 (包括網域) 和暫時密碼，以剛建立的 Azure AD 使用者身分登入 Azure。 

3. 要求系統管理員在出現提示時變更密碼。

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>設定 Azure 自動化來管理 Azure 訂用帳戶

若要讓 Azure 自動化與 Azure AD 通訊，您必須將與 Azure 連線相關聯的認證擷取至 Azure AD。 這些認證的範例包括租用戶識別碼、訂用帳戶識別碼等等。 如需 Azure 與 Azure AD 之間連結的詳細資訊，請參閱[將您的組織連結至 Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops) \(英文\)。

## <a name="create-a-credential-asset"></a>建立認證資產

若可以使用適用於 Azure AD 的 Azure 認證，就可以建立 Azure 自動化認證資產，以安全地儲存 Azure AD 認證，讓 Runbook 和期望狀態設定 (DSC) 指令碼可以加以存取。 您可以使用 Azure 入口網站或 PowerShell Cmdlet 來執行此動作。

### <a name="create-the-credential-asset-in-azure-portal"></a>在 Azure 入口網站中建立認證資產

您可以使用 Azure 入口網站來建立認證資產。 執行此作業的方式是從您的自動化帳戶，於 [共用資源] 下，使用 [認證]。 請參閱 [Azure 自動化中的認證資產](shared-resources/credentials.md)。

### <a name="create-the-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 建立認證資產

若要在 Windows PowerShell 中準備新的認證資產，您的指令碼會先使用指派的使用者名稱和密碼來建立 `PSCredential` 物件。 接著，指令碼會使用這個物件，透過呼叫 [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential?view=azuresmps-4.0.0) \(英文\) Cmdlet 來建立資產。 或者，指令碼可以呼叫 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) Cmdlet 來提示使用者輸入名稱和密碼。 請參閱 [Azure 自動化中的認證資產](shared-resources/credentials.md)。 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>從 Azure 自動化 Runbook 管理 Azure 資源

您可以從使用認證資產的 Azure 自動化 Runbook 來管理 Azure 資源。 以下是 PowerShell Runbook 範例，其會收集用來停止和啟動 Azure 訂用帳戶中虛擬機器的認證資產。 此 Runbook 會先使用 `Get-AutomationPSCredential` 來擷取要用來向 Azure 進行驗證的認證。 然後，其會呼叫 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) Cmdlet，以使用認證連線到 Azure。 指令碼會使用 [Select-AzureSubscription](/powershell/module/servicemanagement/azure.service/select-azuresubscription?view=azuresmps-4.0.0) \(英文\) Cmdlet 來選擇要使用的訂用帳戶。 

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

* 如需認證使用的詳細資訊，請參閱[管理 Azure 自動化中的認證](shared-resources/credentials.md)。
* 如需模組的詳細資訊，請參閱[在 Azure 自動化中管理模組](shared-resources/modules.md)。
* 如果您需要啟動 Runbook，請參閱[在 Azure 自動化中啟動 Runbook](start-runbooks.md)。
* 如需 PowerShell 的詳細資料，請參閱 [PowerShell 文件](/powershell/scripting/overview)。
