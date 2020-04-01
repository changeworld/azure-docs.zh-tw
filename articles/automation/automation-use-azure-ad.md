---
title: 在 Azure 自動化中使用 Azure AD 對 Azure 進行身分驗證
description: 瞭解如何在 Azure 自動化中使用 Azure AD 作為對 Azure 進行身份驗證的提供程式。
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 77476c67761a950430b39d5baddf2c6efd77f1a2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479444"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>在 Azure 自動化中使用 Azure AD 對 Azure 進行身分驗證

[Azure 活動目錄 (AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)服務支援許多管理任務,如使用者管理、域管理和單一登錄配置。 本文介紹如何在 Azure 自動化中使用 Azure AD 作為對 Azure 進行身份驗證的提供者。 

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="installing-azure-ad-modules"></a>安裝 Azure AD 模組

您可以透過下一個 PowerShell 模組開啟 Azure AD:

* 用於圖形的 Azure 活動目錄電源外殼(AzureRM 和 Az 模組)。 Azure 自動化隨 AzureRM 模組及其最近的升級(Az 模組)一起提供。 功能包括使用 Azure AD 使用者 (OrgId) 基於認證身份驗證對 Azure 的非互動式身分驗證。 請參閱[Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76)。

* 適用於 Windows 電源外殼(MSOnline 模組)的 Microsoft Azure 活動目錄。 此模組支援與 Microsoft 線上(包括 Office 365)的交互。

>[!NOTE]
>PowerShell Core 不支援 MSOnline 模組。 要使用模組 cmdlet,必須從 Windows PowerShell 運行它們。 我們鼓勵您將較新的 Azure 活動目錄 PowerShell 用於圖形模組,而不是 MSOnline 模組。 

### <a name="preinstallation"></a>預先安裝

在電腦上安裝 Azure AD 模組之前:

* 卸載 AzureRM/Az 模組和 MSOnline 模組的任何早期版本。 

* 卸載 Microsoft 線上服務登錄助手,以確保新 PowerShell 模組的正確操作。  

### <a name="install-the-azurerm-and-az-modules"></a>安裝 AzureRM 與 Az 模組

>[!NOTE]
>要使用這些模組,您必須將 PowerShell 版本 5.1 或更高版本與 64 位元版本的 Windows 使用。 

1. 安裝 Windows 管理框架 (WMF) 5.1。 請參考[安裝與設定 WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7)。

2. 使用在[Windows 上使用 PowerShellGet 安裝 AzureRm](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0)和/或 Az 的說明。

### <a name="install-the-msonline-module"></a>安裝 MSOnline 模組

>[!NOTE]
>要安裝 MSOnline 模組,您必須是 Office 365 管理員角色的成員。 請參考[有關管理員角色](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)。

1. 確保電腦上啟用了 Microsoft .NET 框架 3.5.x 功能。 您的電腦可能安裝了較新版本,但可以啟用或禁用與舊版本的 .NET Framework 的向後相容性。 

2. 安裝 64 位元版本的[Microsoft 線上服務登入助理](https://www.microsoft.com/download/details.aspx?id=41950)。

3. 以管理員身份運行 Windows PowerShell 以創建提升的 Windows PowerShell 命令提示符。

4. 從[MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0)部署 Azure 活動目錄。

5. 如果系統提示您安裝 NuGet 提供程式,請鍵入 Y 並按 ENTER。

6. 如果系統提示您從[PSGallery](https://www.powershellgallery.com/)安裝模組,請鍵入 Y 並按 ENTER。

### <a name="install-support-for-pscredential"></a>安裝對 PS 認證

Azure 自動化使用[PS認證,](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0)類表示憑據資產。 文稿使用`PSCredential``Get-AutomationPSCredential`cmdlet 檢索物件。 有關詳細資訊,請參閱[Azure 自動化 中的認證資產](shared-resources/credentials.md)。

## <a name="assigning-a-subscription-administrator"></a>分配訂閱管理員

您必須為 Azure 訂閱分配管理員。 此人具有訂閱作用域的所有者角色。 請參考[Azure 自動化 中的基於角色的存取控制](automation-role-based-access-control.md)。 

## <a name="changing-the-azure-ad-users-password"></a>變更 Azure AD 使用者的密碼

要變更 Azure AD 使用者的密碼,請執行以下操作:

1. 註銷 Azure。

2. 讓管理員在 Azure AD 使用者剛剛建立時使用完整使用者名稱(包括網域)和臨時密碼登錄到 Azure。 

3. 在出現提示時要求管理員更改密碼。

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>設定 Azure 自動化以使用 Azure AD 使用者來管理 Azure 訂閱

若要與 Azure 自動化通訊,必須檢索與 Azure 連接到 Azure AD 關聯的認證。 這些憑據的範例包括租戶 ID、訂閱 ID 等。 有關 Azure 與 Azure AD 之間的連線,請參閱[將組織連接到 Azure 的活動目錄](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops)。

## <a name="creating-a-credential-asset"></a>創建認證資產

使用 Azure AD 的 Azure 認證後,是時候建立 Azure 自動化認證資產來安全地儲存 Azure AD 認證,以便 Runbook 和慾望狀態配置 (DSC) 文本可以存取它們。 可以使用 Azure 門戶或 PowerShell cmdlet 執行此操作。

### <a name="create-the-credential-asset-in-azure-portal"></a>在 Azure 門戶中創建認證資產

可以使用 Azure 門戶創建憑據資產。 使用**共用資源**下的**認證從**自動化帳戶執行此操作。 請參閱[Azure 自動化 中的認證資產](shared-resources/credentials.md)。

### <a name="create-the-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 建立認證資產

要在 Windows PowerShell 中準備新的認證資產,文`PSCredential`稿首先使用分配的使用者名和密碼創建物件。 然後,腳本使用此物件通過調用[New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) cmdlet 來創建資產。 或者,文稿可以調用[Get-認證使用者輸入使用者](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7)名和密碼。 請參閱[Azure 自動化 中的認證資產](shared-resources/credentials.md)。 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>從 Azure 自動化執行簿管理 Azure 資源

您可以使用認證資產從 Azure 自動化執行簿管理 Azure 資源。 下面是 PowerShell 執行簿的範例,用於收集用於在 Azure 訂閱中停止和啟動虛擬機器的認證資產。 此 Runbook`Get-AutomationPSCredential`首先用於檢索用於對 Azure 進行身份驗證的認證。 然後,它調用[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) cmdlet 以使用認證連接到 Azure。 該文本使用[選擇 Azure 訂閱](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0)cmdlet 選擇要使用的訂閱。 

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

* 您可以在[Azure 自動化 中的認證資產中找到](shared-resources/credentials.md)有關自動化認證資產的資訊。
* 請參閱[管理 Azure 自動化中的模組](shared-resources/modules.md),瞭解如何使用自動化模組。
* 要瞭解有關可用於在 Azure 自動化中啟動 Runbook 的方法,請參閱[在 Azure 自動化 中啟動 Runbook。](automation-starting-a-runbook.md)
* 有關 PowerShell 的詳細資訊(包括語言參考和學習模組),請參閱[PowerShell 文件](https://docs.microsoft.com/powershell/scripting/overview)。