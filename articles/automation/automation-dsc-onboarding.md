---
title: 以 Azure 自動化狀態設定管理的板載電腦
description: 本文介紹如何使用 Azure 自動化狀態配置設置用於管理的計算機。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 04/15/2020
manager: carmonm
ms.openlocfilehash: 4fc9f701f4f832deb1fed34d7ac9f888521d3830
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406206"
---
# <a name="onboard-machines-for-management-by-azure-automation-state-configuration"></a>以 Azure 自動化狀態設定管理的板載電腦

Azure 自動化狀態配置是雲端或本地資料中心中所需狀態配置 (DSC) 節點的配置管理服務。 您可以通過在**配置管理**下選擇**狀態配置 (DSC)** 來造訪 Azure 入口中的服務。 

借助 Azure 自動化狀態配置,您可以快速輕鬆地從中央安全位置跨數千台電腦實現可擴充性。 您可以輕鬆地對計算機進行板載、為其分配聲明性配置,並查看顯示每台電腦符合指定狀態的報告。

Azure 自動化狀態配置服務是DSC Azure自動化運行簿對PowerShell腳本的腳本。 換句話說，「Azure 自動化」會以協助您管理 Powershell 指令碼的相同方式，同樣協助您管理 DSC 組態。 若要深入了解使用 Azure Automation State Configuration 的優點，請參閱 [Azure Automation State Configuration 概觀](automation-dsc-overview.md)。

可以使用 Azure 自動化狀態設定管理各種電腦:

- Azure 虛擬機器 (VM)
- Azure 虛擬機器 (傳統)
- 本地或 Azure 以外的雲端/虛擬 Windows 電腦(包括 Amazon Web 服務 [AWS] 彈性計算雲 [EC2] 實體 )
- 位於內部部署、Azure 或 Azure 以外之雲端中的實體/虛擬 Linux 機器

如果尚未準備好從雲端管理電腦配置,則可以使用 Azure 自動化狀態配置作為僅報告終結點。 此功能允許您透過 DSC 設定或推送設定,並在 Azure 自動化中查看報告詳細資訊。

> [!NOTE]
> 如果已安裝的 Azure VM 所需狀態配置擴展版本為版本 2.70 或更高版本,則包含使用 Azure 自動化狀態配置管理 Azure VM 不收取額外費用。 有關詳細資訊,請參閱[自動化定價](https://azure.microsoft.com/pricing/details/automation/)。

本文的以下各節概述了如何將以前列出的計算機添加到 Azure 自動化狀態配置。

## <a name="onboard-azure-vms"></a>使 Azure VM 上線

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[Azure 自動化 中的更新 Azure PowerShell 模組](automation-update-azure-modules.md)將模組更新到最新版本。

由於 Azure 擴展以非同步方式運行,因此,我們在本文的疑[難排解 Azure 虛擬機器載入部分](#troubleshoot-azure-virtual-machine-onboarding)中提供了追蹤其進度或排除故障的步驟。

> [!NOTE]
> 將 DSC 部署到 Linux 節點使用 */tmp*資料夾。 安裝模組(`nxautomation`如)在安裝之前將臨時下載以進行驗證。 為確保正確安裝模組,Linux 的日誌分析代理需要 */tmp*資料夾的讀/寫許可權。<br><br>
> Linux 的日誌分析代理作為*omsagent*使用者運行。 要加入*omsagent*使用者的寫入權限`setfacl -m u:omsagent:rwx /tmp`,請執行指令 。

### <a name="onboard-vms-by-using-the-azure-portal"></a>使用 Azure 門戶的板載 VM

透過[Azure 門戶](https://portal.azure.com/)將 Azure VM 新增到 Azure 自動化狀態設定:

1. 轉到要在其中加入 VM 的 Azure 自動化帳戶。 

1. 在 **「狀態設定**」頁上,選擇 **「節點」** 選項卡,然後選擇「**添加**」 。。

1. 選擇要上載的 VM。

1. 如果電腦未安裝 PowerShell 所需狀態擴展,並且電源狀態正在運行,請選擇「**連接**」 。。

1. 在 **「註冊」下**,輸入用例所需的[PowerShell DSC 本地設定管理員 (LCM) 值](/powershell/scripting/dsc/managing-nodes/metaConfig)。 或者,您可以輸入要分配給 VM 的節點配置。

![VM 註冊窗格](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-vms-by-using-azure-resource-manager-templates"></a>使用 Azure 資源管理員樣本的樣本載 VM

可以使用 Azure 資源管理器範本將 VM 部署到 Azure 自動化狀態配置並將其重新上傳。 有關將現有 VM 連接到 Azure 自動化狀態設定的範例樣本,請參考[由所需狀態設定服務管理的伺服器](https://azure.microsoft.com/resources/templates/101-automation-configuration/)。 如果要管理虛擬機規模集,請參閱[由 Azure 自動化管理的虛擬機縮放集配置](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)中的範例範本。

### <a name="onboard-vms-by-using-powershell"></a>使用 PowerShell 的板載 VM

您可以使用 PowerShell 中的[註冊-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet 將 VM 上傳到 Azure 自動化狀態配置。 

> [!NOTE]
>`Register-AzAutomationDscNode` cmdlet 目前僅為運行 Windows 的電腦實現,因為 cmdlet 僅觸發 Windows 擴展。

### <a name="register-vms-across-azure-subscriptions"></a>跨 Azure 訂閱註冊 VM

從其他 Azure 訂閱註冊 VM 的最佳方法是在 Azure 資源管理器部署範本中使用 DSC 擴展。 範例[在「所需的狀態設定」擴充中提供 Azure 資源管理員樣本](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)。

要尋找在樣本中用參數的註冊金鑰和註冊網址,請使用本文中的[註冊部分安全地檢視板載](#onboard-securely-by-using-registration)。

## <a name="onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure"></a>本地或 Azure 以外的雲端中的板載物理/虛擬 Windows 電腦

您可以將在本地或其他雲端環境中(包括 AWS EC2 實例)執行的 Windows 伺服器連接到 Azure 自動化狀態配置。 伺服器必須具有[對 Azure 的出站存取權限](automation-dsc-overview.md#network-planning)。

1. 確保最新版本的[Windows 管理框架 5](https://aka.ms/wmf5latest)安裝在要裝機到 Azure 自動化狀態配置的電腦上。 此外,Windows 管理框架 5 必須安裝在用於載入操作的電腦上。
1. 要建立包含所需 DSC 配置的資料夾,請按照[生成 DSC 配置部分中的](#generate-dsc-metaconfigurations)說明操作。 
1. 要將 PowerShell DSC 中設定遠端應用於要上傳的電腦,請使用以下 cmdlet: 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. 如果無法遠端應用 PowerShell DSC 配置,請將**中設定**資料夾複製到要載入的電腦。 然後添加代碼以在電腦上本地調用[Set-DscLocalManager。](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1)

1. 在 Azure 門戶或使用 cmdlet 中,驗證要上傳的電腦是否顯示為在 Azure 自動化帳戶中註冊的狀態配置節點。

## <a name="onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>本地或 Azure 以外的雲端中的板載物理/虛擬 Linux 電腦

您可以將在本地或其他雲端環境執行的 Linux 伺服器連接到 Azure 自動化狀態配置。 伺服器必須具有[對 Azure 的出站存取權限](automation-dsc-overview.md#network-planning)。

1. 確保最新版本的[Linux PowerShell 所需狀態設定](https://github.com/Microsoft/PowerShell-DSC-for-Linux)安裝在要裝機到 Azure 自動化狀態配置的電腦上。
1. 如果[PowerShell DSC LCM 預設值](/powershell/scripting/dsc/managing-nodes/metaConfig4)與您的用例符合,並且您希望在電腦上板,以便它們同時從 Azure 自動化狀態設定中拉出並報告,請執行以下操作:

   a. 在每台 Linux 電腦上,使用 PowerShell DSC`Register.py`本地配置管理器預設值來將其板載。

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   b. 要尋找自動化帳戶的註冊金鑰和註冊網址,請使用本文的[註冊部分安全地檢視板載](#onboard-securely-by-using-registration)。  
   c. (跳至步驟 4)。
   
1. 如果 PowerShell DSC LCM 預設值與您的用例不匹配,或者如果希望將只向 Azure 自動化狀態配置報告的電腦板載,請按照 a-d 步驟操作。 否則,直接轉到步驟 d。

    a. 要生成包含所需 DSC 元配置的資料夾,請按照[生成 DSC 元配置部分中的](#generate-dsc-metaconfigurations)說明操作。

    b. 確保最新版本的[Windows 管理框架 5](https://aka.ms/wmf5latest)安裝在用於載入的電腦上。

    c. 要將 PowerShell DSC 中設定遠端應用於要上傳的電腦,請新增以下代碼:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

    d. 如果無法遠端應用 PowerShell DSC 配置,請將與遠端電腦對應的元設定從步驟 3a 中描述的資料夾中複製到 Linux 電腦。

1. 添加要在每個 Linux 電腦上調用*Set-DscLocalConfigurationManager.py*本地的代碼,以便將其載入 Azure 自動化狀態配置。

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. 通過使用 Azure 門戶或 cmdlet,請確保要上傳的電腦現在顯示為 Azure 自動化帳戶中註冊的 DSC 節點。

## <a name="generate-dsc-metaconfigurations"></a>建構 DSC 中設定

要將任何電腦連線到 Azure 自動化狀態設定,可以產生[DSC 中設定](/powershell/scripting/dsc/managing-nodes/metaConfig)。 此配置告訴 DSC 代理從 Azure 自動化狀態配置中提取或報告。 可以使用 PowerShell DSC 配置或 Azure 自動化 PowerShell cmdlet 為 Azure 自動化狀態配置生成 DSC 元配置。

> [!NOTE]
> DSC 元配置包含將計算機載入自動化帳戶以進行管理所需的機密。 請務必正確保護您創建的任何 DSC 元配置,或在使用後將其刪除。

對元配置的代理支援由 LCM 控制,LCM 是 Windows PowerShell DSC 引擎。 LCM 在所有目標節點上運行,並負責調用 DSC 元配置腳本中包含的配置資源。 

通過在`ConfigurationRepositoryWeb``ResourceRepositoryWeb`中 根據需要包括代理 URL 的定義和代理認證,可以在元配置中包括`ReportServerWeb`代理支援。 請參考[設定本地端設定管理員](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7)。

### <a name="generate-dsc-metaconfigurations-by-using-a-dsc-configuration"></a>使用 DSC 設定產生 DSC 中設定

1. 在本地環境中的電腦上以管理員身份打開可視化工作室代碼(或您最喜愛的編輯器)。 計算機必須安裝最新版本的 Windows[管理框架 5。](https://aka.ms/wmf5latest)
1. 在本機複製下列指令碼。 此指令碼包含用來建立中繼設定的 PowerShell DSC 設定，以及開始執行中繼設定建立作業的命令。

    > [!NOTE]
    > 狀態配置節點配置名稱在 Azure 門戶中區分大小寫。 如果情況不匹配,節點將不會顯示在 **「節點」** 選項卡下。

   ```powershell
   # The DSC configuration that will generate metaconfigurations.
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

    # Create the metaconfigurations.
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: Edit the following as needed for your use case.
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

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   DscMetaConfigs @Params
   ```

1. 填寫您自動化帳戶的註冊金鑰和 URL，以及要上架的電腦名稱。 所有其他參數都是選擇性的。 要尋找自動化帳戶的註冊金鑰與註冊網址,[請使用註冊部分安全地檢視板載](#onboard-securely-by-using-registration)。

1. 如果希望電腦向 Azure 自動化狀態設定報告 DSC 狀態資訊,但不提取設定或`ReportOnly`PowerShell 模組, 則將參數設定為*true*。

1. 如果未`ReportOnly`設定,計算機將 DSC 狀態資訊報告給 Azure 自動化狀態配置和拉取配置或 PowerShell 模組。 在`ConfigurationRepositoryWeb`中相應地設定`ResourceRepositoryWeb``ReportServerWeb`參數 , 和 塊。

1. 執行指令碼。 現在,您應該有一個稱為*DscMetaConfigs*的工作目錄資料夾 ,其中包含用於已上傳電腦的 PowerShell DSC 配置(作為管理員)。

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-by-using-azure-automation-cmdlets"></a>使用 Azure 自動化 cmdlet 產生 DSC 中設定

如果 PowerShell DSC LCM 預設值與您的用例匹配,並且您希望將電腦從 Azure 自動化狀態配置中拉出並報告,則可以通過使用 Azure 自動化 cmdlet 更簡單地生成所需的 DSC 元配置。

1. 在本地環境中的電腦上以管理員身份打開 PowerShell 主控台或可視化工作室代碼。
1. 使用[連接-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0)連接到 Azure 資源管理員。
1. 從要設置節點的自動化帳戶中,下載要上傳的電腦的 PowerShell DSC 元配置。

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting.
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. 現在,您應該有一個*DscMetaConfigs*資料夾,其中包含要上載的電腦(作為管理員)的PowerShell DSC元配置。

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboard-securely-by-using-registration"></a>使用註冊安全地登機

可以通過 Windows 管理框架 5 DSC 註冊協議將電腦安全地安裝到 Azure 自動化帳戶。 此協定允許 DSC 節點對 PowerShell DSC 拉取伺服器或報表伺服器進行身份驗證,包括 Azure 自動化狀態配置。 節點在註冊 URL 上向伺服器註冊並使用註冊密鑰進行身份驗證。 

在註冊期間,DSC 節點和 DSC 拉/報告伺服器協商一個唯一的證書,用於節點在註冊後對伺服器進行身份驗證。 此過程可防止板載節點相互類比(例如,如果節點受到威脅並惡意運行)。 

註冊后,註冊金鑰不會重新用於身份驗證,並且會從節點中刪除。

要取得狀態設定註冊協定所需的資訊,請轉到 Azure 門戶,並在 **「帳戶設定」** 下選擇 **「密鑰**」。 

![「金鑰」窗格中的 Azure 自動化鍵和網址](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- **註冊網****址 :URL**框中的值。
- **註冊鍵**:**主存取金鑰**框中的值或**輔助存取金鑰**框中的值。 您可以使用任一鍵。

為了增加安全性,您可以隨時在 **「密鑰」** 窗格中重新生成自動化帳戶的主訪問密鑰和輔助存取密鑰。 金鑰重新生成可防止將來的節點註冊使用以前的密鑰。

## <a name="re-register-a-node"></a>重新註冊節點

在 Azure 自動化狀態配置中將電腦註冊為 DSC 節點後,可能需要在將來重新註冊該節點,原因之一:

- **證書續訂**:對於 Windows Server 2019 之前的版本,每個節點會自動協商唯一的身份驗證證書,該證書將在一年後過期。 如果證書過期而不續訂,則節點無法與 Azure 自動化通訊,並且標記為*無回應*。 

  目前,PowerShell DSC 註冊協議無法在證書即將到期時自動續訂證書,您必須在一年後重新註冊節點。 在重新註冊之前,請確保每個節點都運行 Windows 管理框架 5 RTM。 

    重新註冊在證書過期時間 90 天或更少,或在證書過期時間之後的任何時間執行,從而導致生成和使用新證書。 此問題的解決方案包含在 Windows 伺服器 2019 及更高版本中。

- **對 DSC LCM 值的更改**:您可能需要更改在節點初始註冊期間設置的[PowerShell DSC LCM 值](/powershell/scripting/dsc/managing-nodes/metaConfig4)(`ConfigurationMode`例如)。 目前,只能通過重新註冊更改這些 DSC 代理值。 一個例外是分配給節點的節點配置值。 您可以在 Azure 自動化 DSC 中直接更改此值。

通過使用本文檔中描述的任何載入方法,可以重新註冊節點,就像最初註冊節點一樣。 在重新註冊節點之前,無需從 Azure 自動化狀態配置中取消註冊節點。

## <a name="troubleshoot-azure-virtual-machine-onboarding"></a>排除 Azure 虛擬機器載入故障

Azure Automation State Configuration 可讓您輕鬆地將 Azure Windows VM 上架以進行組態管理。 在幕後，Azure VM Desired State Configuration 擴充功能是用來向 Azure Automation State Configuration 註冊 VM。 由於 Azure VM 所需的狀態配置擴展以非同步方式運行,因此追蹤其進度並排除其執行故障可能很重要。

> [!NOTE]
> 任何使用 Azure VM 所需狀態配置擴展將 Azure Windows VM 載入 Azure 自動化狀態配置的方法最多可能需要一個小時才能將其顯示為已註冊。 這種延遲是由 Azure VM 所需狀態配置擴展在 VM 上安裝 Windows 管理框架 5 造成的,這是將 VM 裝載到 Azure 自動化狀態配置所必需的。

要排除故障或查看 Azure VM 所需狀態設定延伸的狀態,請使用以下操作:

1. 在 Azure 門戶中,轉到正在板載的 VM。
2. 在 [設定]**** 底下，選取 [延伸模組]****。 
3. 根據您的作業系統選擇**DSC**或**DSCForLinux。** 
4. 有關擴展的詳細資訊,請選擇「**查看詳細狀態**」。

有關故障排除的詳細資訊,請參閱 Azure[自動化所需狀態設定 (DSC) 的疑難排解問題](./troubleshoot/desired-state-configuration.md)。

## <a name="next-steps"></a>後續步驟

- 要開始,請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)。
- 要瞭解如何編譯 DSC 設定以便將它們分配給目標節點,請參閱[Azure 自動化狀態設定中的編譯設定](automation-dsc-compile.md)。
- 有關 PowerShell cmdlet 引用,請參閱[Az.自動化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)。
- 有關定價資訊,請參閱[Azure 自動化狀態設定定價](https://azure.microsoft.com/pricing/details/automation/)。
- 有關在連續部署導管中使用 Azure 自動化狀態設定的範例,請參考使用[Azure 自動化狀態設定和巧克力 連續部署到虛擬機器](automation-dsc-cd-chocolatey.md)。
