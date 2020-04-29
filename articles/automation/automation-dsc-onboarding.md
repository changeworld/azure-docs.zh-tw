---
title: 將機器上架交由 Azure Automation State Configuration 管理
description: 如何設定電腦以 Azure 自動化狀態設定進行管理
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: d4e008a0bd43f10b01d78a1c388f1ca6fee983ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457734"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>將機器上架交由 Azure Automation State Configuration 管理

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>為什麼要使用 Azure Automation State Configuration 管理機器？

Azure 自動化狀態設定是適用于任何雲端或內部部署資料中心內 Desired State Configuration （DSC）節點的設定管理服務。 在 [設定**管理**] 下選取 [**狀態設定（DSC）** ]，即可在 Azure 入口網站中存取它。 

這項服務可讓您從集中式安全的位置快速輕鬆地進行跨數千部機器的擴充性。 您可以輕鬆地將機器上線、指派宣告式設定，以及查看顯示每台機器相容性的報告，以及您指定的所需狀態。

Azure 自動化狀態設定服務是 DSC Azure 自動化 runbook 與 PowerShell 腳本。 換句話說，「Azure 自動化」會以協助您管理 Powershell 指令碼的相同方式，同樣協助您管理 DSC 組態。 若要深入了解使用 Azure Automation State Configuration 的優點，請參閱 [Azure Automation State Configuration 概觀](automation-dsc-overview.md)。

Azure Automation State Configuration 可以用來管理各種不同的機器：

- Azure 虛擬機器
- Azure 虛擬機器 (傳統)
- 內部部署或 Azure 以外的雲端中的實體/虛擬 Windows 機器（包括 AWS EC2 實例）
- 位於內部部署、Azure 或 Azure 以外之雲端中的實體/虛擬 Linux 機器

如果您還沒準備好從雲端管理電腦設定，您可以使用 Azure 自動化狀態設定作為僅限報告的端點。 這項功能可讓您透過 DSC 設定（推送）設定，並在 Azure 自動化中查看報告詳細資料。

> [!NOTE]
> 如果已安裝的 Azure VM Desired State Configuration 延伸模組版本大於2.70，則以 Azure 自動化狀態設定管理 Azure Vm 時，不需要額外付費。 如需詳細資訊，請參閱[**自動化定價頁面**](https://azure.microsoft.com/pricing/details/automation/)。

本文的下列各節將概述如何讓上列機器上線，以 Azure 自動化狀態設定。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="onboarding-azure-vms"></a>將 Azure Vm 上架

Azure 自動化狀態設定可讓您使用 Azure 入口網站、Azure Resource Manager 範本或 PowerShell，輕鬆將 Azure Vm 上架以進行設定管理。 在幕後，而且沒有系統管理員必須從遠端進入 VM，Azure VM Desired State Configuration 延伸模組會向 Azure 自動化狀態設定註冊 VM。 因為 Azure 擴充功能會以非同步方式執行，所以追蹤其進度或進行疑難排解的步驟會在本文的[疑難排解 Azure 虛擬機器上架](#troubleshooting-azure-virtual-machine-onboarding)一節中提供。

> [!NOTE]
>將 DSC 部署到 Linux 節點使用 [ **/tmp** ] 資料夾。 之類的模組`nxautomation`會暫時下載以進行驗證，然後再將它們安裝在適當的位置。 為確保模組正確安裝，適用于 Linux 的 Log Analytics 代理程式需要 **/tmp**資料夾的讀取/寫入權限。<br><br>
>適用于 Linux 的 Log Analytics 代理程式會`omsagent`以使用者身分執行。 若要將 >寫入權限授`omsagent`與使用者，請執行`setfacl -m u:omsagent:rwx /tmp`命令。

### <a name="onboard-a-vm-using-azure-portal"></a>使用 Azure 入口網站將 VM 上架

若要讓 Azure VM 上線以透過[Azure 入口網站](https://portal.azure.com/)Azure 自動化狀態設定：

1. 流覽至要在其中上架 Vm 的 Azure 自動化帳戶。 

2. 在 [狀態設定] 頁面上，選取 [**節點**] 索引標籤，然後按一下 [**新增**]。

3. 選擇要上架的 VM。

4. 如果電腦未安裝 PowerShell 預期狀態延伸模組，且電源狀態為 [執行中]，請按一下 **[連線]**。

5. 在 [**註冊**] 底下，輸入您的使用案例所需的[PowerShell DSC 本機 Configuration Manager 值](/powershell/scripting/dsc/managing-nodes/metaConfig)。 （選擇性）您可以輸入要指派給 VM 的節點設定。

![上架](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本將 VM 上架

您可以使用 Azure Resource Manager 範本，將 VM 部署並上架至 Azure 自動化狀態設定。 如需將上線現有 VM 以 Azure 自動化狀態設定的範例範本，請參閱[Desired State Configuration 服務管理的伺服器](https://azure.microsoft.com/resources/templates/101-automation-configuration/)。 如果您要管理虛擬機器擴展集，請參閱[Azure 自動化管理的虛擬機器擴展集設定](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)中的範例範本。

### <a name="onboard-machines-using-powershell"></a>使用 PowerShell 將電腦上架

您可以使用 PowerShell 中的[AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) Cmdlet，將 vm 上架到 Azure 自動化狀態設定。 

> [!NOTE]
>此`Register-AzAutomationDscNode` Cmdlet 目前僅適用于執行 windows 的電腦，因為它只會觸發 windows 延伸模組。

### <a name="register-vms-across-azure-subscriptions"></a>在 Azure 訂用帳戶中註冊 Vm

向其他 Azure 訂用帳戶註冊 Vm 的最佳方式，是使用 Azure Resource Manager 部署範本中的 DSC 延伸模組。 [Azure Resource Manager 範本 Desired State Configuration 擴充](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)功能中提供範例。

若要在範本中尋找用來做為參數的註冊金鑰和註冊 URL，請參閱本文中的[使用註冊安全地](#onboarding-securely-using-registration)上線一節。

## <a name="onboarding-physicalvirtual-windows-machines"></a>將實體/虛擬 Windows 機器上架

您可以將執行于內部部署或其他雲端環境（包括 AWS EC2 實例）中的 Windows 伺服器上架到 Azure 自動化狀態設定。 伺服器必須具有[Azure 的輸出存取權](automation-dsc-overview.md#network-planning)。

1. 請確定已在電腦上安裝最新版本的[WMF 5](https://aka.ms/wmf5latest) ，以 Azure 自動化狀態設定上架。 此外，WMF 5 必須安裝在您用於上架操作的電腦上。
1. 遵循[產生 dsc 中繼設定](#generating-dsc-metaconfigurations)一節中的指示，建立包含所需 DSC 中繼設定的資料夾。 
1. 使用下列 Cmdlet，從遠端將 PowerShell DSC 中繼設定套用至您想要上架的電腦。 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. 如果您無法從遠端套用 PowerShell DSC 中繼設定，請將**中繼設定**資料夾複製到您正在上架的電腦。 然後在電腦上的本機新增程式碼以呼叫[DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) 。
1. 使用 Azure 入口網站或 Cmdlet，確認要上架的電腦會顯示為 Azure 自動化帳戶中註冊的狀態設定節點。

## <a name="onboarding-physicalvirtual-linux-machines"></a>將實體/虛擬 Linux 電腦上架

您可以將執行于內部部署或其他雲端環境中的 Linux 伺服器上線，以 Azure 自動化狀態設定。 伺服器必須具有[Azure 的輸出存取權](automation-dsc-overview.md#network-planning)。

1. 請確定已在要上架至 Azure 自動化狀態設定的電腦上安裝[適用于 Linux 的 PowerShell Desired State Configuration](https://github.com/Microsoft/PowerShell-DSC-for-Linux)的最新版本。
2. 如果[POWERSHELL DSC 本機 Configuration Manager 預設值](/powershell/scripting/dsc/managing-nodes/metaConfig4)符合您的使用案例，而且您想要將電腦上架，使其從 Azure 自動化狀態設定提取和報告：

   - 在要上架至 Azure 自動化狀態設定的每一部`Register.py` Linux 電腦上，使用來使用 PowerShell DSC 本機 Configuration Manager 預設值進行上架。

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - 若要尋找自動化帳戶的註冊金鑰和註冊 URL，請參閱本文的[使用註冊安全地](#onboarding-securely-using-registration)上線一節。

3. 如果 PowerShell DSC 本機 Configuration Manager （LCM）預設值不符合您的使用案例，或者您想要將僅報告的機器上架到 Azure 自動化狀態設定，請遵循步驟4-7。 否則，請直接跳至步驟7。

4. 遵循[產生 dsc 中繼設定](#generating-dsc-metaconfigurations)一節中的指示，產生包含所需 DSC 中繼設定的資料夾。

5. 請確定在用於上架的電腦上已安裝最新版本的[WMF 5](https://aka.ms/wmf5latest) 。

6. 如下所示新增程式碼，以從遠端將 PowerShell DSC 中繼設定套用至您想要上架的電腦。

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. 如果您無法從遠端套用 PowerShell DSC 中繼設定，請從步驟4中所述的資料夾，將對應到遠端電腦的中繼設定複製到 Linux 電腦。

8. 新增要在每`Set-DscLocalConfigurationManager.py`個 Linux 電腦本機上呼叫的程式碼，以上架至 Azure 自動化狀態設定。

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. 使用 Azure 入口網站或 Cmdlet，確保要上架的電腦現在會顯示為在您的 Azure 自動化帳戶中註冊的 DSC 節點。

## <a name="generating-dsc-metaconfigurations"></a>產生 DSC 中繼設定

若要將任何電腦上架至 Azure 自動化狀態設定，您可以產生[DSC](/powershell/scripting/dsc/managing-nodes/metaConfig)中繼中繼。 此設定會指示 DSC 代理程式從和/或報表提取 Azure 自動化狀態設定。 您可以使用 PowerShell DSC 設定或 Azure 自動化 PowerShell Cmdlet，為 Azure 自動化狀態設定產生 DSC 中繼設定。

> [!NOTE]
> DSC 中繼設定包含將電腦上架至進行管理之自動化帳戶的機密資料。 請務必適當地保護您所建立的任何 DSC 中繼設定，或在使用後將它們刪除。

中繼設定的 Proxy 支援是由 LCM （也就是 Windows PowerShell DSC 引擎）控制。 LCM 會在所有目標節點上執行，並負責呼叫包含在 DSC 中繼設定腳本中的設定資源。 您可以視需要在`ConfigurationRepositoryWeb`、 `ResourceRepositoryWeb`和`ReportServerWeb`區塊中加入 proxy URL 的定義和 proxy 認證，以在中繼中繼中包含 proxy 支援。 請參閱設定[本機 Configuration Manager](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7)。

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>使用 DSC 設定產生 DSC 中繼設定

1. 在本機環境中的電腦上，以系統管理員身分開啟 VSCode （或您偏好的編輯器）。 電腦必須安裝最新版本的 [WMF 5](https://aka.ms/wmf5latest) 。
1. 在本機複製下列指令碼。 此指令碼包含用來建立中繼設定的 PowerShell DSC 設定，以及開始執行中繼設定建立作業的命令。

    > [!NOTE]
    > 狀態設定節點設定名稱會在 Azure 入口網站中區分大小寫。 如果案例不相符，節點將不會顯示在 [**節點**] 索引標籤底下。

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. 填寫您自動化帳戶的註冊金鑰和 URL，以及要上架的電腦名稱。 所有其他參數都是選擇性的。 若要尋找自動化帳戶的註冊金鑰和註冊 URL，請參閱[使用註冊安全地](#onboarding-securely-using-registration)上線一節。

1. 如果您想要讓機器向 Azure 自動化狀態設定報告 DSC 狀態資訊，但不是提取設定或 PowerShell 模組，請`ReportOnly`將參數設定為 [true]。

1. 如果`ReportOnly`未設定，電腦會將 DSC 狀態資訊報告為 Azure 自動化狀態設定並提取設定或 PowerShell 模組。 在`ConfigurationRepositoryWeb`、和`ResourceRepositoryWeb` `ReportServerWeb`區塊中，適當地設定參數。

1. 執行指令碼。 您現在應該會有一個名為**為 dscmetaconfigs**的工作目錄資料夾，其中包含要上架之電腦的 PowerShell DSC 中繼設定（以系統管理員身分）。

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>使用 Azure 自動化 Cmdlet 產生 DSC 中繼設定

如果 PowerShell DSC LCM 預設值符合您的使用案例，而且您想要讓電腦上線，以從 Azure 自動化狀態設定提取和報告，您可以使用 Azure 自動化 Cmdlet，更輕鬆地產生所需的 DSC 中繼設定。

1. 在本機環境中的電腦上，以系統管理員身分開啟 PowerShell 主控台或 VSCode。
2. 使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0)連接到 Azure Resource Manager。
3. 從您要在其中設定節點的自動化帳戶中，下載您想要上架之電腦的 PowerShell DSC 中繼設定。

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. 您現在應該有一個**為 dscmetaconfigs**資料夾，其中包含要上架之電腦的 PowerShell DSC 中繼設定（以系統管理員身分）。

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>使用註冊安全地上線

機器可以透過 WMF 5 DSC 註冊通訊協定安全地上架至 Azure 自動化帳戶。 此通訊協定可讓 DSC 節點向 PowerShell DSC 提取或報表伺服器進行驗證，包括 Azure 自動化狀態設定。 節點會在註冊 URL 向伺服器註冊，並使用註冊金鑰進行驗證。 在註冊期間，DSC 節點和 DSC 提取/報表伺服器會協調節點的唯一憑證，以便在註冊後用來向伺服器進行驗證。 此程式可防止上架節點彼此模擬，例如，如果節點遭到入侵，且有惡意的行為。 註冊之後，註冊金鑰不會再次用於驗證，而且會從節點中刪除。

您可以從 Azure 入口網站中 [帳戶設定]**** 底下的 [金鑰]**** 取得 State Configuration 註冊通訊協定所需的資訊。 

![Azure 自動化金鑰和 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- [註冊 URL] 是 [金鑰] 頁面上的 [URL] 欄位。
- [註冊金鑰] 是 [金鑰] 頁面上 [**主要存取金鑰**] 欄位或 [**次要存取金鑰**] 欄位的值。 可以使用這兩個金鑰。

為了增加安全性，您可以隨時在 [金鑰] 頁面上重新產生自動化帳戶的主要和次要存取金鑰。 重新產生金鑰可避免未來的節點註冊使用先前的金鑰。

## <a name="re-registering-a-node"></a>重新註冊節點

在 Azure 自動化狀態設定中將機器註冊為 DSC 節點之後，有幾個原因會導致您可能需要在未來重新註冊該節點。

- **憑證更新。** 針對 windows server 2019 之前的 Windows Server 版本，每個節點都會自動針對在一年後到期的驗證，協調唯一的憑證。 如果憑證過期但沒有更新，則節點無法與 Azure 自動化通訊，並標示`Unresponsive`為。 目前，當憑證即將到期時，PowerShell DSC 註冊通訊協定無法自動更新憑證，而且您必須在一年後重新註冊節點。 重新註冊之前，請確定每個節點正在執行 WMF 5 RTM。 

    重新註冊會從憑證到期時間執行90天或更短，或在憑證到期時間之後的任何時間點，會產生並使用新的憑證。 此問題的解決方式包含在 Windows Server 2019 和更新版本中。

- **DSC LCM 值的變更。** 您可能需要變更在節點初始註冊期間設定的[POWERSHELL DSC LCM 值](/powershell/scripting/dsc/managing-nodes/metaConfig4)，例如`ConfigurationMode`。 目前，您只能透過重新註冊來變更這些 DSC 代理程式值。 其中一個例外狀況是指派給節點的節點設定值。 您可以直接在 Azure 自動化 DSC 中變更此項。

您可以使用本檔中所述的任何上線方法，來重新註冊節點，就像您一開始註冊節點一樣。 您不需要先從 Azure 自動化狀態設定取消註冊節點，再重新註冊。

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>疑難排解 Azure 虛擬機器上架

Azure Automation State Configuration 可讓您輕鬆地將 Azure Windows VM 上架以進行組態管理。 在幕後，Azure VM Desired State Configuration 擴充功能是用來向 Azure Automation State Configuration 註冊 VM。 因為 Azure VM Desired State Configuration 擴充功能會以非同步方式執行，所以追蹤其進度和疑難排解其執行可能很重要。

> [!NOTE]
> 將 Azure Windows VM 上架到使用 Azure VM Desired State Configuration 延伸模組 Azure 自動化狀態設定的任何方法，最多可能需要一小時的時間，Azure 自動化才會將其顯示為已註冊。 這項延遲是因為 Azure VM Desired State Configuration 延伸模組在 VM 上安裝 WMF 5，這是將 VM 上架以 Azure 自動化狀態設定所需的。

若要疑難排解或查看 Azure VM Desired State Configuration 擴充功能的狀態：

1. 在 [Azure 入口網站中，流覽至要上架的 VM。
2. 按一下 [**設定**] 底下的 [**擴充**功能]。 
3. 現在請選取 [ **DSC** ] 或 [ **DSCForLinux**]，視您的作業系統而定。 
4. 如需詳細資訊，您可以按一下 [檢視詳細狀態] ****。

如需疑難排解的詳細資訊，請參閱[Azure 自動化 Desired State Configuration （DSC）的疑難排解問題](./troubleshoot/desired-state-configuration.md)。

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[Azure 自動化狀態設定](automation-dsc-getting-started.md)使用者入門。
- 若要瞭解如何編譯 DSC 設定，讓您可以將它們指派給目標節點，請參閱[在 Azure 自動化狀態設定中編譯](automation-dsc-compile.md)設定。
- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 如需定價資訊，請參閱[Azure 自動化狀態設定定價](https://azure.microsoft.com/pricing/details/automation/)。
- 如需在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用範例：使用 Azure 自動化狀態設定和 Chocolatey 持續部署至虛擬機器](automation-dsc-cd-chocolatey.md)。
