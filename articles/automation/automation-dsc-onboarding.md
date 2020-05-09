---
title: Azure 自動化狀態設定啟用電腦管理
description: 如何設定電腦以 Azure 自動化狀態設定進行管理
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: 52cd72d1144fa2acad993e927d49545d645d596f
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993742"
---
# <a name="enable-machines-for-management-by-azure-automation-state-configuration"></a>Azure 自動化狀態設定啟用電腦管理

本主題說明如何設定您的電腦，以使用 Azure 自動化狀態設定進行管理。 如需這項服務的詳細資訊，請參閱[Azure 自動化狀態設定總覽](automation-dsc-overview.md)。

## <a name="enable-azure-vms"></a>啟用 Azure VM

Azure 自動化狀態設定可讓您使用 Azure 入口網站、Azure Resource Manager 範本或 PowerShell，輕鬆地啟用 Azure Vm 以進行設定管理。 在幕後，而且沒有系統管理員必須從遠端進入 VM，Azure VM Desired State Configuration 延伸模組會向 Azure 自動化狀態設定註冊 VM。 因為 Azure 擴充功能會以非同步方式執行，所以[針對狀態設定的 VM 設定進行疑難排解](#troubleshoot-vm-setup-for-state-configuration)時，會提供追蹤其進度或疑難排解的步驟。

> [!NOTE]
>將 DSC 部署到 Linux 節點使用 [ **/tmp** ] 資料夾。 之類的模組`nxautomation`會暫時下載以進行驗證，然後再將它們安裝在適當的位置。 為確保模組正確安裝，適用于 Linux 的 Log Analytics 代理程式需要 **/tmp**資料夾的讀取/寫入權限。<br><br>
>適用于 Linux 的 Log Analytics 代理程式會`omsagent`以使用者身分執行。 若要將 >寫入權限授`omsagent`與使用者，請執行`setfacl -m u:omsagent:rwx /tmp`命令。

### <a name="enable-a-vm-using-azure-portal"></a>使用 Azure 入口網站啟用 VM

若要讓 Azure VM 能夠透過[Azure 入口網站](https://portal.azure.com/)狀態設定：

1. 流覽至要在其中啟用 Vm 的 Azure 自動化帳戶。 

2. 在 [狀態設定] 頁面上，選取 [**節點**] 索引標籤，然後按一下 [**新增**]。

3. 選擇要啟用的 VM。

4. 如果電腦未安裝 PowerShell 預期狀態延伸模組，且電源狀態為 [執行中]，請按一下 **[連線]**。

5. 在 [**註冊**] 底下，輸入您的使用案例所需的[PowerShell DSC 本機 Configuration Manager 值](/powershell/scripting/dsc/managing-nodes/metaConfig)。 （選擇性）您可以輸入要指派給 VM 的節點設定。

![啟用 VM](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本啟用 VM

您可以使用 Azure Resource Manager 範本，為狀態設定安裝和啟用 VM。 如需可讓現有 VM 進行狀態設定的範例範本，請參閱[Desired State Configuration 服務管理的伺服器](https://azure.microsoft.com/resources/templates/101-automation-configuration/)。 如果您要管理虛擬機器擴展集，請參閱[Azure 自動化管理的虛擬機器擴展集設定](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)中的範例範本。

### <a name="enable-machines-using-powershell"></a>使用 PowerShell 啟用機器

您可以在 PowerShell 中使用[AzAutomationDscNode 指令程式](/powershell/module/az.automation/register-azautomationdscnode)來啟用狀態設定的 vm。 

> [!NOTE]
>此`Register-AzAutomationDscNode` Cmdlet 目前僅適用于執行 windows 的電腦，因為它只會觸發 windows 延伸模組。

### <a name="register-vms-across-azure-subscriptions"></a>在 Azure 訂用帳戶中註冊 Vm

向其他 Azure 訂用帳戶註冊 Vm 的最佳方式，是使用 Azure Resource Manager 部署範本中的 DSC 延伸模組。 [Azure Resource Manager 範本 Desired State Configuration 擴充](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)功能中提供範例。

若要在範本中尋找用來作為參數的註冊金鑰和註冊 URL，請參閱[使用註冊安全地啟用電腦](#enable-machines-securely-using-registration)。

## <a name="enable-physicalvirtual-windows-machines"></a>啟用實體/虛擬 Windows 機器

您可以啟用在內部部署或其他雲端環境（包括 AWS EC2 實例）中執行的 Windows 伺服器，以 Azure 自動化狀態設定。 伺服器必須具有[Azure 的輸出存取權](automation-dsc-overview.md#network-planning)。

1. 請確定電腦上已安裝最新版本的[WMF 5](https://aka.ms/wmf5latest) ，以啟用狀態設定。 此外，您要用來啟用電腦的電腦上必須安裝 WMF 5。
1. 遵循[產生 DSC 中繼設定](#generate-dsc-metaconfigurations)中的指示，建立包含所需 DSC 中繼設定的資料夾。 
1. 使用下列 Cmdlet，從遠端將 PowerShell DSC 中繼設定套用至要啟用的電腦。 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. 如果您無法從遠端套用 PowerShell DSC 中繼設定，請將**中繼設定**資料夾複製到您要啟用的電腦。 然後在電腦上的本機新增程式碼以呼叫[DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) 。
1. 使用 Azure 入口網站或 Cmdlet，確認電腦顯示為 Azure 自動化帳戶中註冊的狀態設定節點。

## <a name="enable-physicalvirtual-linux-machines"></a>啟用實體/虛擬 Linux 機器

您可以啟用在內部部署或其他雲端環境中執行的 Linux 伺服器，以進行狀態設定。 伺服器必須具有[Azure 的輸出存取權](automation-dsc-overview.md#network-planning)。

1. 請確定已在電腦上安裝[適用于 Linux 的 PowerShell Desired State Configuration](https://github.com/Microsoft/PowerShell-DSC-for-Linux)的最新版本，以啟用狀態設定。
2. 如果[POWERSHELL DSC 本機 Configuration Manager 預設值](/powershell/scripting/dsc/managing-nodes/metaConfig4)符合您的使用案例，而且您想要啟用機器，使其能夠從狀態設定提取和報告：

   - 在要啟用的每部 Linux 電腦上`Register.py` ，使用來啟用具有 PowerShell DSC 本機 Configuration Manager 預設值的電腦。

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - 若要尋找自動化帳戶的註冊金鑰和註冊 URL，請參閱[使用註冊安全地啟用電腦](#enable-machines-securely-using-registration)。

3. 如果 PowerShell DSC 本機 Configuration Manager （LCM）預設值不符合您的使用案例，或者您想要啟用只報告 Azure 自動化狀態設定的電腦，請遵循步驟4-7。 否則，請直接跳至步驟7。

4. 遵循[產生 dsc 中繼設定](#generate-dsc-metaconfigurations)一節中的指示，產生包含所需 DSC 中繼設定的資料夾。

5. 請確定電腦上已安裝最新版本的[WMF 5](https://aka.ms/wmf5latest) ，以啟用您的電腦以進行狀態設定。

6. 如下所示新增程式碼，以從遠端將 PowerShell DSC 中繼設定套用至要啟用的電腦。

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. 如果您無法從遠端套用 PowerShell DSC 中繼設定，請從步驟4中所述的資料夾，將對應到遠端電腦的中繼設定複製到 Linux 電腦。

8. 新增要在每`Set-DscLocalConfigurationManager.py`個 Linux 電腦本機上呼叫的程式碼，以啟用狀態設定。

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. 使用 Azure 入口網站或 Cmdlet，確定要啟用的電腦現在會顯示為在您的 Azure 自動化帳戶中註冊的 DSC 節點。

## <a name="generate-dsc-metaconfigurations"></a>產生 DSC 中繼設定

若要啟用任何電腦以進行狀態設定，您可以產生[DSC](/powershell/scripting/dsc/managing-nodes/metaConfig)中繼中繼。 此設定會指示 DSC 代理程式從和/或報表提取 Azure 自動化狀態設定。 您可以使用 PowerShell DSC 設定或 Azure 自動化 PowerShell Cmdlet，為 Azure 自動化狀態設定產生 DSC 中繼設定。

> [!NOTE]
> DSC 中繼設定包含在自動化帳戶中啟用電腦以進行管理所需的秘密。 請務必適當地保護您所建立的任何 DSC 中繼設定，或在使用後將它們刪除。

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

1. 填入您的自動化帳戶的註冊金鑰和 URL，以及要啟用的電腦名稱稱。 所有其他參數都是選擇性的。 若要尋找自動化帳戶的註冊金鑰和註冊 URL，請參閱[使用註冊安全地啟用電腦](#enable-machines-securely-using-registration)。

1. 如果您想要讓機器向 Azure 自動化狀態設定報告 DSC 狀態資訊，但不是提取設定或 PowerShell 模組，請`ReportOnly`將參數設定為 [true]。

1. 如果`ReportOnly`未設定，電腦會將 DSC 狀態資訊報告為 Azure 自動化狀態設定並提取設定或 PowerShell 模組。 在`ConfigurationRepositoryWeb`、和`ResourceRepositoryWeb` `ReportServerWeb`區塊中，適當地設定參數。

1. 執行指令碼。 您現在應該會有一個名為**為 dscmetaconfigs**的工作目錄資料夾，其中包含要啟用之電腦的 PowerShell DSC 中繼設定（以系統管理員身分）。

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>使用 Azure 自動化 Cmdlet 產生 DSC 中繼設定

如果 PowerShell DSC LCM 預設值符合您的使用案例，而且您想要讓機器可以從 Azure 自動化狀態設定提取和報告，您可以使用 Azure 自動化 Cmdlet，更輕鬆地產生所需的 DSC 中繼設定。

1. 在本機環境中的電腦上，以系統管理員身分開啟 PowerShell 主控台或 VSCode。
2. 使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0)連接到 Azure Resource Manager。
3. 從您要在其中設定節點的自動化帳戶，下載您想要啟用之電腦的 PowerShell DSC 中繼設定。

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

1. 您現在應該有一個**為 dscmetaconfigs**資料夾，其中包含要啟用之電腦的 PowerShell DSC 中繼設定（以系統管理員身分）。

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>使用註冊安全地啟用機器

您可以透過 WMF 5 DSC 註冊通訊協定，安全地為 Azure 自動化帳戶啟用電腦。 此通訊協定可讓 DSC 節點向 PowerShell DSC 提取或報表伺服器進行驗證，包括 Azure 自動化狀態設定。 節點會在註冊 URL 向伺服器註冊，並使用註冊金鑰進行驗證。 在註冊期間，DSC 節點和 DSC 提取/報表伺服器會協調節點的唯一憑證，以便在註冊後用來向伺服器進行驗證。 此程式會防止已啟用的節點彼此模擬，例如，如果節點遭到入侵，且有惡意的行為。 註冊之後，註冊金鑰不會再次用於驗證，而且會從節點中刪除。

您可以從 Azure 入口網站中 [帳戶設定]**** 底下的 [金鑰]**** 取得 State Configuration 註冊通訊協定所需的資訊。 

![Azure 自動化金鑰和 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- [註冊 URL] 是 [金鑰] 頁面上的 [URL] 欄位。
- [註冊金鑰] 是 [金鑰] 頁面上 [**主要存取金鑰**] 欄位或 [**次要存取金鑰**] 欄位的值。 可以使用這兩個金鑰。

為了增加安全性，您可以隨時在 [金鑰] 頁面上重新產生自動化帳戶的主要和次要存取金鑰。 重新產生金鑰可避免未來的節點註冊使用先前的金鑰。

## <a name="re-register-a-node"></a>重新註冊節點

在 Azure 自動化狀態設定中將機器註冊為 DSC 節點之後，有幾個原因會導致您可能需要在未來重新註冊該節點。

- **憑證更新。** 針對 windows server 2019 之前的 Windows Server 版本，每個節點都會自動針對在一年後到期的驗證，協調唯一的憑證。 如果憑證過期但沒有更新，則節點無法與 Azure 自動化通訊，並標示`Unresponsive`為。 目前，當憑證即將到期時，PowerShell DSC 註冊通訊協定無法自動更新憑證，而且您必須在一年後重新註冊節點。 重新註冊之前，請確定每個節點正在執行 WMF 5 RTM。 

    重新註冊會從憑證到期時間執行90天或更短，或在憑證到期時間之後的任何時間點，會產生並使用新的憑證。 此問題的解決方式包含在 Windows Server 2019 和更新版本中。

- **DSC LCM 值的變更。** 您可能需要變更在節點初始註冊期間設定的[POWERSHELL DSC LCM 值](/powershell/scripting/dsc/managing-nodes/metaConfig4)，例如`ConfigurationMode`。 目前，您只能透過重新註冊來變更這些 DSC 代理程式值。 其中一個例外狀況是指派給節點的節點設定值。 您可以直接在 Azure 自動化 DSC 中變更此項。

您可以使用本檔中所述的任何方法，重新註冊節點，就像您一開始註冊節點一樣。 您不需要先從 Azure 自動化狀態設定取消註冊節點，再重新註冊。

## <a name="troubleshoot-vm-setup-for-state-configuration"></a>針對狀態設定的 VM 設定進行疑難排解

狀態設定可讓您輕鬆地啟用 Azure Windows Vm 來進行設定管理。 在幕後，Azure VM Desired State Configuration 擴充功能是用來向 Azure Automation State Configuration 註冊 VM。 因為 Azure VM Desired State Configuration 擴充功能會以非同步方式執行，所以追蹤其進度和疑難排解其執行可能很重要。

> [!NOTE]
> 為使用 Azure VM Desired State Configuration 延伸模組的狀態設定啟用 Azure Windows Vm 的任何方法，最多可能需要一小時的時間，Azure 自動化才會顯示已註冊的 Vm。 此延遲的原因是因為 Azure VM Desired State Configuration 延伸模組在 VM 上安裝 WMF 5，這是啟用 Vm 以進行狀態設定的必要項。

若要疑難排解或查看 Azure VM Desired State Configuration 擴充功能的狀態：

1. 在 [Azure 入口網站中，流覽至要啟用的 VM。
2. 按一下 [**設定**] 底下的 [**擴充**功能]。 
3. 現在請選取 [ **DSC** ] 或 [ **DSCForLinux**]，視您的作業系統而定。 
4. 如需詳細資訊，您可以按一下 [檢視詳細狀態] ****。

如需有關疑難排解的詳細資訊，請參閱針對[Azure 自動化狀態設定進行疑難排解](./troubleshoot/desired-state-configuration.md)。

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[Azure 自動化狀態設定](automation-dsc-getting-started.md)使用者入門。
- 若要瞭解如何編譯 DSC 設定，讓您可以將它們指派給目標節點，請參閱[在 Azure 自動化狀態設定中編譯](automation-dsc-compile.md)設定。
- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 如需定價資訊，請參閱[Azure 自動化狀態設定定價](https://azure.microsoft.com/pricing/details/automation/)。
- 如需在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用範例：使用 Azure 自動化狀態設定和 Chocolatey 持續部署至虛擬機器](automation-dsc-cd-chocolatey.md)。
