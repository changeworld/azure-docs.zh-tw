---
title: 啟用 Azure 自動化狀態設定
description: 本文說明如何設定機器以交由「Azure 自動化狀態設定」管理。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: ae268534a18a921cca012881fa172261c7ba1063
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186396"
---
# <a name="enable-azure-automation-state-configuration"></a>啟用 Azure 自動化狀態設定

本主題說明如何設定機器以交由「Azure 自動化狀態設定」管理。 如需此服務的詳細資料，請參閱 [Azure 自動化狀態設定概觀](automation-dsc-overview.md)。

## <a name="enable-azure-vms"></a>啟用 Azure VM

「Azure 自動化狀態設定」可讓您使用 Azure 入口網站、Azure Resource Manager 範本或 PowerShell，輕鬆啟用 Azure VM 來支援組態管理。 管理員不需要從遠端連至 VM，Azure VM Desired State Configuration 擴充會在幕後向「Azure 自動化狀態設定」註冊 VM。 因為 Azure 擴充以非同步方式執行，[檢查 VM 設定的狀態](#check-status-of-vm-setup)中提供追蹤進度的步驟。

> [!NOTE]
>將 DSC 部署到 Linux 節點時會使用 **/tmp** 資料夾。 系統會先暫時下載 `nxautomation` 之類的模組來驗證，然後才安裝到適當的位置。 為了確保模組正確安裝，適用於 Linux 的 Log Analytics 代理程式需要 **/tmp** 資料夾的讀取/寫入權限。<br><br>
>適用於 Linux 的 Log Analytics 代理程式以 `omsagent` 使用者身分執行。 若要將寫入權限授與 `omsagent` 使用者，請執行 `setfacl -m u:omsagent:rwx /tmp` 命令。

### <a name="enable-a-vm-using-azure-portal"></a>使用 Azure 入口網站啟用 VM

若要透過 [Azure 入口網站](https://portal.azure.com/)啟用 Azure VM 來支援「狀態設定」：

1. 瀏覽至您要在其中啟用 VM 的 Azure 自動化帳戶。 

2. 在 [狀態設定] 頁面上，選取 [節點] 索引標籤，然後按一下 [新增]。

3. 選擇要啟用的 VM。

4. 如果機器未安裝 PowerShell Desired State 擴充，但電源狀態為執行中，請按一下 [連線]。

5. 在 [註冊] 下，輸入您的使用案例所需的 [PowerShell DSC Local Configuration Manager 值](/powershell/scripting/dsc/managing-nodes/metaConfig)。 (選擇性) 您可以輸入要指派給 VM 的節點設定。

![啟用 VM](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本啟用 VM

您可以使用 Azure Resource Manager 範本來安裝和啟用 VM，以支援「狀態設定」。 如需可啟用現有 VM 來支援「狀態設定」的範本範例，請參閱[由 Desired State Configuration 服務管理的伺服器](https://azure.microsoft.com/resources/templates/101-automation-configuration/)。 如果您要管理虛擬機器擴展集，請參閱[由 Azure 自動化管理的虛擬機器擴展集設定](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)中的範本範例。

### <a name="enable-machines-using-powershell"></a>使用 PowerShell 啟用機器

您可以在 PowerShell 中使用 [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) Cmdlet，以啟用 VM 來支援「狀態設定」。 

> [!NOTE]
>`Register-AzAutomationDscNode` Cmdlet 只會觸發 Windows 擴充，所以目前僅針對執行 Windows 的機器來實作。

### <a name="register-vms-across-azure-subscriptions"></a>跨 Azure 訂用帳戶來註冊 VM

若要從其他 Azure 訂用帳戶來註冊 VM，最佳方式是在 Azure Resource Manager 部署範本中使用 DSC 擴充。 [Azure Resource Manager 範本中的 Desired State Configuration 擴充](../virtual-machines/extensions/dsc-template.md)中提供範例。

若要尋找在範本中作為參數的註冊金鑰和註冊 URL，請參閱[使用註冊安全地啟用機器](#enable-machines-securely-using-registration)。

## <a name="enable-physicalvirtual-windows-machines"></a>啟用實體/虛擬 Windows 機器

您可以啟用在內部部署或其他雲端環境 (包括 AWS EC2 執行個體) 中執行的 Windows 伺服器，以支援「Azure 自動化狀態設定」。 伺服器必須具有[對 Azure 的輸出存取](automation-dsc-overview.md#network-planning)。

1. 在您要啟用來支援「狀態設定」的機器上，請確定已安裝最新版的 [WMF 5](https://aka.ms/wmf5latest)。 此外，在您要用來啟用機器的電腦上，必須安裝 WMF 5。
1. 遵循[產生 DSC 中繼設定](#generate-dsc-metaconfigurations)中的指示建立資料夾，其中包含所需的 DSC 中繼設定。 
1. 使用下列 Cmdlet，從遠端將 PowerShell DSC 中繼設定套用至要啟用的機器。 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. 如果您無法從遠端套用 PowerShell DSC 中繼設定，請將 **metaconfigurations** 資料夾複製到您要啟用的機器上。 然後新增程式碼，以在機器本機呼叫 [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1)。
1. 使用 Azure 入口網站或 Cmdlet，驗證機器是否顯示為 Azure 自動化帳戶中註冊的「狀態設定」節點。

## <a name="enable-physicalvirtual-linux-machines"></a>啟用實體/虛擬 Linux 機器

您可以啟用在內部部署或其他雲端環境中執行的 Linux 伺服器，以支援「狀態設定」。 伺服器必須具有[對 Azure 的輸出存取](automation-dsc-overview.md#network-planning)。

1. 在要啟用來支援「狀態設定」的機器上，請確定已安裝最新版的 [PowerShell Desired State Configuration for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux)。
2. 如果 [PowerShell DSC Local Configuration Manager 預設值](/powershell/scripting/dsc/managing-nodes/metaConfig4)符合您的使用案例，而且您想要啟用機器，讓機器從「狀態設定」提取並向此服務報告：

   - 在要啟用的每部 Linux 機器上，使用 `Register.py`，以 PowerShell DSC Local Configuration Manager 預設值啟用機器。

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - 若要尋找 Azure 自動化帳戶的註冊金鑰和註冊 URL，請參閱[使用註冊安全地啟用機器](#enable-machines-securely-using-registration)。

3. 如果 PowerShell DSC Local Configuration Manager (LCM) 預設值不符合您的使用案例，或您想要啟用機器以只向「Azure 自動化狀態設定」報告，請遵循步驟 4-7。 否則，請直接跳至步驟 7。

4. 遵循[產生 DSC 中繼設定](#generate-dsc-metaconfigurations)一節的指示產生資料夾，其中包含所需的 DSC 中繼設定。

5. 在用來啟用機器以支援「狀態設定」的電腦上，請確定已安裝最新版的 [WMF 5](https://aka.ms/wmf5latest)。

6. 新增如下的程式碼，從遠端將 PowerShell DSC 中繼設定套用至要啟用的機器。

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. 如果您無法從遠端套用 PowerShell DSC 中繼設定，請將對應於遠端電腦的中繼設定，從步驟 4 所述的資料夾複製到 Linux 機器上。

8. 新增程式碼，在每一部要啟用以支援「狀態設定」的 Linux 機器本機呼叫 `Set-DscLocalConfigurationManager.py`。

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. 使用 Azure 入口網站或 Cmdlet，確定要啟用的機器現在顯示為 Azure 自動化帳戶中註冊的 DSC 節點。

## <a name="generate-dsc-metaconfigurations"></a>產生 DSC 中繼設定

若要啟用任何機器以支援「狀態設定」，您可以產生 [DSC 中繼設定](/powershell/scripting/dsc/managing-nodes/metaConfig)。 此設定指示 DSC 代理程式從「Azure 自動化狀態設定」提取和/或向此服務報告。 您可以使用 PowerShell DSC 設定或 Azure 自動化 PowerShell Cmdlet，以產生「Azure 自動化狀態設定」的 DSC 中繼設定。

> [!NOTE]
> 若要啟用自動化帳戶中的機器來管理，DSC 中繼設定包含所需的秘密。 請務必適當地保護您所建立的任何 DSC 中繼設定，或在使用後將它們刪除。

中繼設定的 Proxy 支援由 [Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7) (Windows PowerShell DSC 引擎) 所控制。 LCM 在所有目標節點上執行，負責呼叫 DSC 中繼設定指令碼中包含的設定資源。 若要將 Proxy 支援加入中繼設定，您可以視需要在 `ConfigurationRepositoryWeb`、`ResourceRepositoryWeb` 和 `ReportServerWeb` 區塊中加入 `ProxyURL` 和 `ProxyCredential` 屬性的定義。 `ProxyURL = "http://172.16.3.6:3128";` 是 URL 設定的例子。 `ProxyCredential` 屬性設定為 `PSCredential` 物件，如[在 Azure 自動化中管理認證](shared-resources/credentials.md)所述。 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>使用 DSC 設定產生 DSC 中繼設定

1. 在本機環境的機器上，以系統管理員身分開啟 VSCode (或您喜愛的編輯器)。 電腦必須安裝最新版本的 [WMF 5](https://aka.ms/wmf5latest) 。
1. 在本機複製下列指令碼。 此指令碼包含用來建立中繼設定的 PowerShell DSC 設定，以及開始執行中繼設定建立作業的命令。

    > [!NOTE]
    > 在入口網站中，「狀態設定節點設定」名稱區分大小寫。 如果大小寫不相符，則該節點不會出現在 [節點] 索引標籤下。

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

1. 填寫自動化帳戶的註冊金鑰和 URL，以及所要啟用機器的名稱。 所有其他參數都是選擇性的。 若要尋找 Azure 自動化帳戶的註冊金鑰和註冊 URL，請參閱[使用註冊安全地啟用機器](#enable-machines-securely-using-registration)。

1. 如果您希望機器向「Azure 自動化狀態設定」報告 DSC 狀態資訊，但不要提取設定或 PowerShell 模組，請將 `ReportOnly` 參數設定為 true。

1. 如果未設定 `ReportOnly`，機器會向「Azure 自動化狀態設定」報告 DSC 狀態資訊，並提取設定或 PowerShell 模組。 在 `ConfigurationRepositoryWeb`、`ResourceRepositoryWeb` 和 `ReportServerWeb` 區塊中，相應地設定參數。

1. 執行指令碼。 您現在應該有一個名為 **DscMetaConfigs** 的工作目錄資料夾，其中包含所要啟用機器 (以系統管理員身分) 的 PowerShell DSC 中繼設定。

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>使用 Azure 自動化 Cmdlet 產生 DSC 中繼設定

如果 PowerShell DSC LCM 預設值符合您的使用案例，而且您想要啟用機器從 「Azure 自動化狀態設定」提取並向此服務報告，您可以使用 Azure 自動化 Cmdlet，以更輕鬆產生所需的 DSC 中繼設定。

1. 在本機環境的機器上，以系統管理員身分開啟 PowerShell 主控台或 VSCode。
2. 使用 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) 連線至 Azure Resource Manager。
3. 從您要在其中設定節點的自動化帳戶，針對您想要啟用的機器下載 PowerShell DSC 中繼設定。

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

1. 您現在應該有 **DscMetaConfigs** 資料夾，其中包含所要啟用機器 (以系統管理員身分) 的 PowerShell DSC 中繼設定。

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>使用註冊安全地啟用機器

您可以透過 WMF 5 DSC 註冊通訊協定，安全地為 Azure 自動化帳戶啟用機器。 此通訊協定可讓 DSC 節點向 PowerShell DSC 提取或報表伺服器進行驗證，包括 Azure 自動化狀態設定。 節點會透過註冊 URL 向伺服器註冊，並使用註冊金鑰進行驗證。 在註冊期間，DSC 節點和 DSC 提取/報表伺服器會交涉唯一憑證，讓節點在註冊之後向伺服器進行驗證。 此流程可避免已啟用的節點彼此模擬，例如節點遭到入侵且具有惡意行為。 註冊之後，註冊金鑰不會再次用於驗證，而是從節點中刪除。

您可以從 Azure 入口網站中 [帳戶設定] 底下的 [金鑰] 取得 State Configuration 註冊通訊協定所需的資訊。 

![Azure 自動化金鑰和 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- 註冊 URL 是 [金鑰] 頁面上的 URL 欄位。
- 註冊金鑰是 [金鑰] 頁面上的 [主要存取金鑰] 欄位或 [次要存取金鑰] 欄位的值。 可以使用這兩個金鑰。

為了增加安全性，您可以隨時在 [金鑰] 頁面上重新產生自動化帳戶的主要和次要存取金鑰。 重新產生金鑰可避免未來註冊節點時使用先前的金鑰。

## <a name="re-register-a-node"></a>重新註冊節點

將機器註冊為「Azure 自動化狀態設定」中的 DSC 節點之後，可能有幾個原因而需要在未來重新註冊該節點。

- **憑證更新**。 針對 Windows Server 2019 之前的 Windows Server 版本，每個節點會自動交涉唯一的驗證憑證，有效期限一年。 如果憑證過期但沒有更新，則節點無法與 Azure 自動化通訊，還會標示為 `Unresponsive`。 目前，當憑證即將到期時，PowerShell DSC 註冊通訊協定無法自動更新憑證，您必須在一年之後重新註冊節點。 重新註冊之前，請確定每個節點都執行 WMF 5 RTM。 

    如果在憑證到期時間之前的 90 天內，或在憑證到期時間之後的任何時間，才重新註冊，則會產生新的憑證來使用。 Windows Server 2019 和更新版本中已解決此問題。

- **變更 DSC LCM 值**。 您可能需要變更在節點初始註冊期間設定的 [PowerShell DSC LCM 值](/powershell/scripting/dsc/managing-nodes/metaConfig4)，例如 `ConfigurationMode`。 目前，您只能透過重新註冊來變更這些 DSC 代理程式值。 但指派給節點的「節點設定」值是例外。 您可以直接在 Azure 自動化 DSC 中變更此值。

您可以使用本文所述的任何方法，像最初註冊節點一樣來重新註冊節點。 重新註冊節點之前，不需要從「Azure 自動化狀態設定」取消註冊節點。

## <a name="check-status-of-vm-setup"></a>檢查 VM 設定的狀態

「狀態設定」可讓您輕鬆啟用 Azure Windows VM 來支援組態管理。 在幕後，Azure VM Desired State Configuration 擴充功能是用來向 Azure Automation State Configuration 註冊 VM。 因為 Azure VM Desired State Configuration 擴充以非同步方式執行，您可能必須追蹤進度並排解執行時的問題。

> [!NOTE]
> 使用任何方法來啟用 Azure Windows VM 以支援「狀態設定」時 (使用 Azure VM Desired State Configuration 擴充)，最多可能需要經過一小時，Azure 自動化才會顯示 VM 已註冊。 此延遲起因於 Azure VM Desired State Configuration 擴充在 VM 上安裝 WMF 5，但為了啟用 VM 來支援「狀態設定」，有此必要。

若要檢視 Azure VM Desired State Configuration 擴充的狀態：

1. 在 Azure 入口網站中，巡覽至要啟用的 VM。
2. 在 [設定] 下，按一下 [擴充]。 
3. 現在，視作業系統而定，選取 [DSC] 或 [DSCForLinux]。 
4. 如需詳細資訊，您可以按一下 [檢視詳細狀態] 。

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)。
- 若要了解如何編譯 DSC 設定以指派給目標節點，請參閱[在 Azure 自動化狀態設定中編譯 DSC 設定](automation-dsc-compile.md)。
- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
- 如需價格資訊，請參閱 [Azure 自動化狀態設定的價格](https://azure.microsoft.com/pricing/details/automation/)。
- 如需在持續部署管線中使用「Azure 自動化狀態設定」的範例，請參閱[使用 Chocolatey 設定持續部署](automation-dsc-cd-chocolatey.md)。
- 如需疑難排解資訊，請參閱[對 Azure 自動化狀態設定進行疑難排解](./troubleshoot/desired-state-configuration.md)。
