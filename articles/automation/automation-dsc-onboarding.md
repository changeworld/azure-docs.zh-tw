---
title: 將機器上架交由 Azure Automation State Configuration 管理
description: 如何使用 Azure 自動化狀態配置設置用於管理的電腦
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: 0bf70b73098427847c73b4dd962d56d44fe6ee2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283203"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>將機器上架交由 Azure Automation State Configuration 管理

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>為什麼要使用 Azure Automation State Configuration 管理機器？

Azure 自動化狀態配置是任何雲或本地資料中心中所需狀態配置 （DSC） 節點的建構管理服務。 通過在**建構管理**下選擇**狀態配置 （DSC），** 在 Azure 門戶中訪問它。 

此服務可在中央、安全的位置快速輕鬆地跨數千台電腦進行可擴充性。 您可以輕鬆地對電腦進行板載，為其分配聲明性配置，並查看顯示每台電腦是否符合您指定的所需狀態的報告。

Azure 自動化狀態佈建服務是 DSC Azure 自動化運行簿對 PowerShell 腳本的腳本。 換句話說，「Azure 自動化」會以協助您管理 Powershell 指令碼的相同方式，同樣協助您管理 DSC 組態。 若要深入了解使用 Azure Automation State Configuration 的優點，請參閱 [Azure Automation State Configuration 概觀](automation-dsc-overview.md)。

Azure Automation State Configuration 可以用來管理各種不同的機器：

- Azure 虛擬機器
- Azure 虛擬機器 (傳統)
- 本地或 Azure 以外的雲（包括 AWS EC2 實例）的物理/虛擬 Windows 電腦
- 位於內部部署、Azure 或 Azure 以外之雲端中的實體/虛擬 Linux 機器

如果尚未準備好從雲管理電腦配置，則可以使用 Azure 自動化狀態配置作為僅報告終結點。 此功能允許您通過 DSC 設置（推送）配置，並在 Azure 自動化中查看報告詳細資訊。

> [!NOTE]
> 如果已安裝的 Azure VM 所需狀態配置擴展版本大於 2.70，則包含使用 Azure 自動化狀態建構管理 Azure VM 不收取額外費用。 有關詳細資訊，請參閱[**自動化定價頁**](https://azure.microsoft.com/pricing/details/automation/)。

本文的以下各節概述了如何將上面列出的電腦板載到 Azure 自動化狀態配置。

## <a name="onboarding-azure-vms"></a>載入 Azure VM

Azure 自動化狀態配置允許您使用 Azure 門戶、Azure 資源管理器範本或 PowerShell 輕鬆將 Azure VM 上載進行建構管理。 在引擎蓋下，無需管理員遠端連線到 VM，Azure VM 所需的狀態配置擴展將 VM 註冊為 Azure 自動化狀態配置。 由於 Azure 擴展以非同步方式運行，因此在本文的"[故障排除 Azure 虛擬機器載入"](#troubleshooting-azure-virtual-machine-onboarding)部分中提供了跟蹤其進度或故障排除的步驟。

> [!NOTE]
>將 DSC 部署到 Linux 節點使用 **/tmp**資料夾。 在將模組`nxautomation`（如模組安裝到其適當位置）之前臨時下載以進行驗證。 為確保模組正確安裝，Linux 的日誌分析代理需要 **/tmp**資料夾的讀/寫許可權。<br><br>
>Linux 的日誌分析代理以使用者身份`omsagent`運行。 要向`omsagent`使用者授予>寫入權限，請運行 命令`setfacl -m u:omsagent:rwx /tmp`。

### <a name="onboard-a-vm-using-azure-portal"></a>使用 Azure 門戶的 VM 板載

通過[Azure 門戶](https://portal.azure.com/)將 Azure VM 與 Azure 自動化狀態配置一起：

1. 導航到 Azure 自動化帳戶，該帳戶將 VM 板載。 

2. 在"狀態配置"頁上，選擇 **"節點"** 選項卡，然後按一下"**添加**"。

3. 選擇要上載的 VM。

4. 如果電腦未安裝 PowerShell 所需的狀態擴展，並且電源狀態正在運行，請按一下"**連接**"。

5. 在 **"註冊"下**，輸入用例所需的[PowerShell DSC 本地組態管理員值](/powershell/scripting/dsc/managing-nodes/metaConfig)。 或者，您可以輸入要分配給 VM 的節點配置。

![上架](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>使用 Azure 資源管理器範本在 VM 上

您可以使用 Azure 資源管理器範本將 VM 部署到 Azure 自動化狀態配置並將其重新上載。 有關將現有 VM 板出 Azure 自動化狀態配置的示例範本[，請參閱由所需狀態佈建服務管理的伺服器](https://azure.microsoft.com/resources/templates/101-automation-configuration/)。 如果要管理 VM 縮放集，請參閱 Azure 自動化[管理的 VM 規模集配置](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)中的示例範本。

### <a name="onboard-machines-using-powershell"></a>使用 PowerShell 的板載電腦

您可以使用 PowerShell 中的[註冊-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) Cmdlet 將 VM 上載到 Azure 自動化狀態配置。 

> [!NOTE]
>`Register-AzAutomationDscNode` Cmdlet 目前僅為運行 Windows 的電腦實現，因為它僅觸發 Windows 擴展。

### <a name="register-vms-across-azure-subscriptions"></a>跨 Azure 訂閱註冊 VM

從其他 Azure 訂閱註冊 VM 的最佳方法是在 Azure 資源管理器部署範本中使用 DSC 擴展。 示例[在"所需的狀態配置"擴展中提供 Azure 資源管理器範本](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)。

要查找在範本中用作參數的註冊金鑰和註冊 URL，請參閱本文中的"[使用註冊"部分安全地載入](#onboarding-securely-using-registration)。

## <a name="onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>在本地或 Azure 以外的雲（包括 AWS EC2 實例）中安裝物理/虛擬 Windows 電腦

您可以將在本地或其他雲環境中運行的 Windows 伺服器連接到 Azure 自動化狀態配置。 伺服器必須具有[對 Azure 的出站存取權限](automation-dsc-overview.md#network-planning)。

1. 確保最新版本的[WMF 5](https://aka.ms/wmf5latest)安裝在要裝機到 Azure 自動化狀態配置的電腦上。 此外，WMF 5 必須安裝在您用於載入操作的電腦上。
1. 按照[生成 DSC 元配置](#generating-dsc-metaconfigurations)部分中的說明創建包含所需 DSC 元配置的資料夾。 
1. 使用以下 Cmdlet 將 PowerShell DSC 元配置遠端應用於要上載的電腦。 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. 如果無法遠端應用 PowerShell DSC 元配置，請將**元配置**資料夾複製到要載入的電腦。 然後添加代碼在電腦上`Set-DscLocalConfigurationManager`本地調用。
1. 使用 Azure 門戶或 Cmdlet，驗證要上載的電腦是否顯示為在 Azure 自動化帳戶中註冊的狀態配置節點。

## <a name="onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>在本地或 Azure 以外的雲中安裝物理/虛擬 Linux 電腦

您可以將在本地或其他雲環境中運行的 Linux 伺服器連接到 Azure 自動化狀態配置。 伺服器必須具有[對 Azure 的出站存取權限](automation-dsc-overview.md#network-planning)。

1. 確保最新版本的[Linux PowerShell 所需狀態配置](https://github.com/Microsoft/PowerShell-DSC-for-Linux)安裝在要裝機到 Azure 自動化狀態配置的電腦上。
2. 如果[PowerShell DSC 本地組態管理員預設值](/powershell/scripting/dsc/managing-nodes/metaConfig4)與您的用例匹配，並且您希望在電腦上板，以便它們同時從電腦中提取並報告到 Azure 自動化狀態配置：

   - 在每台 Linux 電腦上，使用 PowerShell DSC`Register.py`本地組態管理員預設值進行板載。

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - 要查找自動化帳戶的註冊金鑰和註冊 URL，請參閱[使用註冊功能安全地載入](#onboarding-securely-using-registration)（如果本文）。

3. 如果 PowerShell DSC 本地組態管理員 （LCM） 預設值與您的用例不匹配，或者您希望將僅向 Azure 自動化狀態配置報告的電腦板載，請按照步驟 4-7 執行。 否則，直接執行步驟 7。

4. 按照[生成 DSC 元配置](#generating-dsc-metaconfigurations)部分的說明生成包含所需 DSC 元配置的資料夾。

5. 確保最新版本的[WMF 5](https://aka.ms/wmf5latest)安裝在用於載入的電腦上。

6. 按照如下方式添加代碼，將 PowerShell DSC 元配置遠端應用於要上載的電腦。

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. 如果無法遠端應用 PowerShell DSC 元配置，請將與遠端電腦對應的元配置從步驟 4 中描述的資料夾中複製到 Linux 電腦。

8. 將要在每個`Set-DscLocalConfigurationManager.py`Linux 電腦上本地調用的代碼添加到 Azure 自動化狀態配置的板載。

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. 使用 Azure 門戶或 Cmdlet，請確保要上載的電腦現在顯示為 Azure 自動化帳戶中註冊的 DSC 節點。

## <a name="generating-dsc-metaconfigurations"></a>產生 DSC 中繼設定

要將任何電腦連接到 Azure 自動化狀態配置，可以生成[DSC 元配置](/powershell/scripting/dsc/managing-nodes/metaConfig)。 此配置告訴 DSC 代理從和/或向 Azure 自動化狀態配置提取和/或報告。 您可以使用 PowerShell DSC 配置或 Azure 自動化 PowerShell Cmdlet 為 Azure 自動化狀態配置生成 DSC 元配置。

> [!NOTE]
> DSC 中繼設定包含將電腦上架至進行管理之自動化帳戶的機密資料。 請務必適當地保護您所建立的任何 DSC 中繼設定，或在使用後將它們刪除。

對元配置的代理支援由 LCM 控制，LCM 是 Windows PowerShell DSC 引擎。 LCM 在所有目標節點上運行，並負責調用 DSC 元配置腳本中包含的配置資源。 通過在`ConfigurationRepositoryWeb`中`ResourceRepositoryWeb`根據需要包括代理 URL 的定義和代理憑據，可以在元配置中包括代理支援。 `ReportServerWeb` 請參閱[配置本地組態管理員](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7)。

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>使用 DSC 配置生成 DSC 元配置

1. 在本地環境中的電腦上以管理員身份打開 VSCode（或您最喜愛的編輯器）。 電腦必須安裝最新版本的 [WMF 5](https://aka.ms/wmf5latest) 。
1. 在本機複製下列指令碼。 此指令碼包含用來建立中繼設定的 PowerShell DSC 設定，以及開始執行中繼設定建立作業的命令。

    > [!NOTE]
    > 狀態配置節點配置名稱在 Azure 門戶中區分大小寫。 如果情況不匹配，節點將不會顯示在 **"節點"** 選項卡下。

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

1. 填寫您自動化帳戶的註冊金鑰和 URL，以及要上架的電腦名稱。 所有其他參數都是選擇性的。 要查找自動化帳戶的註冊金鑰和註冊 URL，請參閱[使用註冊功能安全地載入](#onboarding-securely-using-registration)。

1. 如果希望電腦向 Azure 自動化狀態配置報告 DSC 狀態資訊，但不希望拔取配置或 PowerShell`ReportOnly`模組，則將參數設置為 true。

1. 如果未`ReportOnly`設置，電腦將 DSC 狀態資訊報告給 Azure 自動化狀態配置和拉取配置或 PowerShell 模組。 在`ConfigurationRepositoryWeb`中相應地設置 參數`ResourceRepositoryWeb`，`ReportServerWeb`和 塊。

1. 執行指令碼。 現在，您應該有一個稱為**DscMetaConfigs**的工作目錄資料夾，其中包含用於已上載電腦的 PowerShell DSC 元配置（作為管理員）。

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>使用 Azure 自動化 Cmdlet 生成 DSC 元配置

如果 PowerShell DSC LCM 預設值與您的用例匹配，並且您希望將電腦從 Azure 自動化狀態配置中拉出並報告，則可以更簡單地使用 Azure 自動化 Cmdlet 生成所需的 DSC 元配置。

1. 在本地環境中的電腦上以管理員身份打開 PowerShell 主控台或 VSCode。
2. 使用 `Connect-AzAccount` 連線到 Azure Resource Manager
3. 從要在其中設置節點的自動化帳戶下載要上載的電腦的 PowerShell DSC 元配置。

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. 現在應該有一個名為**DscMetaConfigs 的**資料夾，其中包含用於已上載電腦的 PowerShell DSC 元配置（作為管理員）。

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>使用註冊安全地載入

電腦可以通過 WMF 5 DSC 註冊協定安全地登上 Azure 自動化帳戶。 此協定允許 DSC 節點對 PowerShell DSC 拉取伺服器或報表伺服器進行身份驗證，包括 Azure 自動化狀態配置。 節點在註冊 URL 上向伺服器註冊並使用註冊金鑰進行身份驗證。 在註冊期間，DSC 節點和 DSC 拉/報表服務器協商一個唯一的證書，用於節點在註冊後對伺服器進行身份驗證。 此過程可防止板載節點相互類比，例如，如果節點受到威脅並惡意行為。 註冊後，註冊金鑰不再用於身份驗證，並從節點中刪除。

您可以從 Azure 入口網站中 [帳戶設定]**** 底下的 [金鑰]**** 取得 State Configuration 註冊通訊協定所需的資訊。 

![Azure 自動化金鑰和 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- 註冊 URL 是"金鑰"頁上的 URL 欄位。
- 註冊鍵是 **"金鑰"頁上的主便捷鍵**欄位或**輔助便捷鍵**欄位的值。 可以使用這兩個金鑰。

為了增加安全性，您可以隨時在"金鑰"頁上重新生成自動化帳戶的主訪問金鑰和輔助訪問金鑰。 金鑰重新生成可防止將來的節點註冊使用以前的金鑰。

## <a name="re-registering-a-node"></a>重新註冊節點

在 Azure 自動化狀態配置中將電腦註冊為 DSC 節點後，可能需要在將來重新註冊該節點有幾個原因。

- **證書續訂。** 對於 Windows Server 2019 之前的 Windows 伺服器版本，每個節點會自動協商唯一的身份驗證憑證，該證書將在一年後過期。 如果證書過期而不續訂，則節點無法與 Azure 自動化通信並標記為`Unresponsive`。 目前，PowerShell DSC 註冊協定無法在證書即將到期時自動續訂證書，您必須在一年後重新註冊節點。 在重新註冊之前，請確保每個節點都運行 WMF 5 RTM。 

    重新註冊在證書過期時間 90 天或更短的時間執行，或在證書過期時間之後的任何時間，導致生成和使用新證書。 此問題的解決方案包含在 Windows 伺服器 2019 及更高版本中。

- **對 DSC LCM 值的更改。** 您可能需要更改在節點初始註冊期間設置的[PowerShell DSC LCM 值](/powershell/scripting/dsc/managing-nodes/metaConfig4)，例如。 `ConfigurationMode` 目前，您只能通過重新註冊來更改這些 DSC 代理值。 一個例外是分配給節點的節點配置值。 您可以在 Azure 自動化 DSC 中直接更改此更改。

您可以使用本文檔中描述的任何載入方法，以最初註冊節點的方式重新註冊節點。 在重新註冊節點之前，不需要從 Azure 自動化狀態配置中取消註冊節點。

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>疑難排解 Azure 虛擬機器上架

Azure Automation State Configuration 可讓您輕鬆地將 Azure Windows VM 上架以進行組態管理。 在幕後，Azure VM Desired State Configuration 擴充功能是用來向 Azure Automation State Configuration 註冊 VM。 由於 Azure VM 所需的狀態配置擴展以非同步方式運行，因此跟蹤其進度並排除其執行故障可能很重要。

> [!NOTE]
> 任何使用 Azure VM 所需狀態配置擴展將 Azure Windows VM 載入 Azure 自動化狀態配置的方法最多可能需要一個小時才能將其顯示為已註冊。 此延遲是由於 Azure VM 所需狀態配置擴展在 VM 上安裝 WMF 5，這是將 VM 裝載到 Azure 自動化狀態配置所必需的。

要疑難排解或查看 Azure VM 所需狀態配置擴展的狀態，請使用以下操作：

1. 在 Azure 門戶中，導航到正在上載的 VM。
2. 按一下 **"設置"** 下的 **"擴展**"。 
3. 現在選擇**DSC**或**DSCForLinux，** 這取決於你的作業系統。 
4. 如需詳細資訊，您可以按一下 [檢視詳細狀態] ****。

有關故障排除的詳細資訊，請參閱 Azure[自動化所需狀態配置 （DSC） 的故障排除問題](./troubleshoot/desired-state-configuration.md)。

## <a name="next-steps"></a>後續步驟

- 要開始，請參閱[開始使用 Azure 自動化狀態配置](automation-dsc-getting-started.md)。
- 要瞭解如何編譯 DSC 配置以便將它們分配給目標節點，請參閱[在 Azure 自動化狀態配置中編譯配置](automation-dsc-compile.md)。
- 有關 PowerShell Cmdlet 引用，請參閱[Azure 自動化狀態配置 Cmdlet](/powershell/module/az.automation#automation)。
- 有關定價資訊，請參閱[Azure 自動化狀態配置定價](https://azure.microsoft.com/pricing/details/automation/)。
- 有關在連續部署管道中使用 Azure 自動化狀態配置的示例，請參閱[使用示例：使用 Azure 自動化狀態配置對虛擬機器的連續部署和巧克力](automation-dsc-cd-chocolatey.md)。
