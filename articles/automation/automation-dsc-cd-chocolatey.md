---
title: Azure 自動化狀態 設定 持續部署與巧克力
description: 使用 Azure 自動化狀態配置與巧克力包管理器描述 DevOps 連續部署。 包括包含完整的 JSON 資源管理器範本和 PowerShell 源的範例。
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 79eaac74fd4475a613c0309476a12292e400dbaa
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010965"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>使用自動化狀態設定和巧克力提供虛擬機器的持續部署

在 DevOps 世界中,有許多工具可幫助處理連續整合管道中的各個點。 Azure 自動化[狀態配置](automation-dsc-overview.md)是 DevOps 團隊可以使用的選項的可喜新補充。 

Azure 自動化是Microsoft Azure 中的託管服務,允許您使用Runbook、節點和共享資源(如憑據、計劃和全域變數)自動執行各種任務。 Azure 自動化狀態配置擴展了此自動化功能,以包括 PowerShell 所需的狀態配置 (DSC) 工具。 以下提供清楚的[概觀](automation-dsc-overview.md)。

本文演示如何為 Windows 計算機設置連續部署 (CD)。 您可以輕鬆地擴展技術,以在角色中根據需要包括盡可能多的 Windows 計算機,例如網站,然後從那裡轉到其他角色。

![IaaS VM 的持續部署](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="at-a-high-level"></a>概括而言

這裏發生了相當多的事情,但幸運的是,它可以分解為兩個主要過程:

- 撰寫程式碼並測試，然後針對系統的主要和次要版本，建立並發佈安裝封裝。
- 建立和管理在包中安裝和執行代碼的 VM。  

這兩個核心進程都到位后,在創建和部署新版本時,可以輕鬆地在 VM 上自動更新包。

## <a name="component-overview"></a>元件概觀

[像apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool)這樣的包管理器在Linux世界中是眾所周知的,但在Windows世界卻不是那麼廣為人知。
[巧克力](https://chocolatey.org/)就是這樣的東西,斯科特·漢塞爾曼的[博客上](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx)關於這個話題是一個很好的介紹。 簡而言之,Chocolatey 允許您使用命令列將包從中央存儲庫安裝到 Windows 作業系統上。 您可以建立和管理您自己的儲存機制，而 Chocolatey 可以從您指定的任何數量的儲存機制來安裝封裝。

[電源外殼 DSC](/powershell/scripting/dsc/overview/overview))是一個 PowerShell 工具,允許您聲明所需的電腦配置。 例如,如果您希望安裝巧克力、安裝 IIS、打開埠 80 以及安裝網站版本 1.0.0,DSC 本地配置管理員 (LCM) 實現該配置。 DSC 拉取伺服器為您的電腦保存配置存儲庫。 每台電腦上的 LCM 會定期檢查電腦的組態是否符合已儲存的組態。 它可能報告狀態，也可能嘗試讓電腦回復到符合已儲存的組態。 您可以編輯提取伺服器上儲存的組態，讓一台電腦或一群電腦符合已變更的組態。

DSC 資源是具有特定功能的程式碼模組,例如管理網路、活動目錄或 SQL Server。 Chocolatey DSC 資源知道如何存取 NuGet 伺服器 (還有其他)、下載封裝、安裝封裝...等等。 [PowerShell 資源庫](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)中有許多其他 DSC 資源。
這些模組會安裝到您的 Azure 自動化狀態設定提取伺服器 (由您安裝)，供您的組態使用。

資源管理器範本提供了生成基礎結構的聲明性方法,例如網路、子網、網路安全和路由、負載均衡器、NIC、VM等。 本文將資源管理員部署[article](../azure-resource-manager/management/deployment-models.md)模型(聲明性)與 Azure 服務管理 (ASM 或經典) 部署模型(命令)進行比較。 本文包括核心資源提供程式(計算、存儲和網路)的討論。

資源管理員範本的一個關鍵功能是,它在預配時能夠將 VM 擴展安裝到 VM 中。 VM 延伸具有特定功能,例如運行自定義文稿、安裝防病毒軟體和運行DSC配置腳本。 有許多其他類型的 VM 延伸模組。

## <a name="quick-trip-around-the-diagram"></a>圖表速覽

從頂部開始,您可以編寫代碼、構建代碼、測試代碼,然後創建一個安裝包。 Chocolatey 可以處理各種類型的安裝封裝，例如 MSI、MSU、ZIP 等。 如果 Chocolatey 的本機功能不能滿足,則 PowerShell 完全有能力進行實際安裝。 將包放入可到達的某個位置 — 包存儲庫。 這個使用範例使用 Azure blob 儲存體帳戶中的公用資料夾，但可以是任何位置。 Chocolatey 可自然搭配 NuGet 伺服器和其他一些工具，一起管理封裝中繼資料。 [這篇文章](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) 說明選項。 這個使用範例使用 NuGet。 Nuspec 是封裝的中繼資料。 Nuspec 被編譯「到 NuPkg 的,並存儲在 NuGet 伺服器中。 當設定按名稱請求包並引用 NuGet 伺服器時,VM 上的巧克力 DSC 資源會抓取該包並為您安裝。 您也可以要求特定版本的封裝。

在圖片的左下角,有一個 Azure 資源管理器範本。 在此使用範例中,VM 擴展將 VM 註冊為 Azure 自動化狀態配置拉取伺服器作為節點。 組態儲存在提取伺服器中。
實際上,它存儲了兩次:一次為純文本,一次編譯為 MOF 檔。 在 Azure 門戶中,MOF 是「節點配置」(而不是簡單的」配置」。)。 它是與節點關聯的專案,因此節點將瞭解其配置。 下列詳細資料示範如何將節點組態指派給節點。

創建 nuspec、編譯它並將其儲存在 NuGet 伺服器中是一件小事。 您已開始管理 VM。 

採取持續部署的下一步需要設置一次拉取伺服器,註冊節點(一次),並在那裡創建和存儲初始配置。 然後,當包升級並部署到存儲庫時,您只需要根據需要刷新拉取伺服器中的配置和節點配置。 

如果您不是從資源管理器範本開始,則沒關係。 有 PowerShell 可説明您在拉取伺服器和所有其他功能中註冊 VM。 有關詳細資訊,請參閱本文:[由 Azure 自動化狀態設定進行管理的載入電腦](automation-dsc-onboarding.md)。

## <a name="about-the-usage-example"></a>關於使用範例

本文中的使用示例從 Azure 庫中的通用 Windows Server 2012 R2 映射中的 VM 開始。 您可以從任何預存的映像開始，再從那裡使用 DSC 組態進行調整。
不過，變更已併入影像的組態，要比使用 DSC 動態更新組態難得多。

將這項技巧運用在 VM 時，不需要使用 Resource Manager 範本和 VM 擴充功能。 此外，VM 不在 Azure 上也能由 CD 管理。 只需要在 VM 上安裝 Chocolatey，並設定 LCM 使其得知提取伺服器所在位置即可。

在正在生產的 VM 上更新包時,需要在安裝更新時使該 VM 退出旋轉。 視情況而定，作法會有很大的差異。 例如，如果 VM 在 Azure 負載平衡器後方，您可以加入自訂探查。 更新 VM 時，讓探查端點傳回 400。 可在組態中進行必要調整來引起此變更，但更新完成時，此調整會恢復成傳回 200。

此使用範例的完整原始檔位於 GitHub 上的 [Visual Studio 專案](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) 。

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>第一步:設定拉取伺服器和自動化帳戶

在已驗證的 (`Connect-AzAccount`) PowerShell 命令列上：(設定提取伺服器可能需要幾分鐘的時間)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

您可以將自動化帳戶放入以下任意區域(也稱為位置):美國東部 2、美國中南部、美國弗吉尼亞州、西歐、東南亞、日本東部、印度中部和澳大利亞東南部、加拿大中部、北歐。

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>第二步:對資源管理員樣本進行 VM 擴充調整

此 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)提供 VM 註冊的詳細資料 (使用 PowerShell DSC VM 延伸模組)。
此步驟會將新的 VM 註冊到提取伺服器的狀態設定節點清單中。 這項註冊的一部分會指定要套用至節點的節點組態。 此節點配置尚不一定存在於拉取伺服器中,因此步驟 4 是首次完成該步驟的位置,這很好。 但在步驟 2 中，您必須決定節點名稱和組態名稱。 在此使用範例中，節點名稱是 'isvbox'，組態名稱是 'ISVBoxConfig'。 因此，節點組態名稱 (將在 DeploymentTemplate.json 中指定) 是 'ISVBoxConfig.isvbox'。

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>第三步:將所需的 DSC 資源加入到拉取伺服器

PowerShell 資源庫會自動將 DSC 資源安裝到您的 Azure 自動化帳戶。
導航到所需的資源,然後單擊"部署到 Azure 自動化"按鈕。

![PowerShell 資源庫範例](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

最近添加到 Azure 門戶的另一種技術允許您提取新模組或更新現有模組。 單擊"自動化帳戶資源"、"資產"磁貼,最後單擊"模組"磁貼。 "流覽庫"圖示允許您查看庫中的模組清單,深入瞭解詳細資訊並最終導入自動化帳戶。 這是讓您的模組隨時保持最新狀態的絕佳方法。 而且，匯入功能會檢查與其他模組的相依性以確保所有模組都保持同步。

或者，您可以使用手動方法。 適用於 Windows 電腦的 PowerShell 整合模組的資料夾結構，與 Azure 自動化所需的資料夾結構稍有不同。
您需要稍微調整一下。 但是這並不難,而且每個資源只完成一次(除非您希望將來升級它)。有關創作 PowerShell 整合模組的詳細資訊,請參閱本文:為[Azure 自動化創作整合模組](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- 將您需要的模組安裝在工作站，如下所示：
  - 安裝 [Windows Management Framework v5](https://aka.ms/wmf5latest) (Windows 10 不需安裝)
  - `Install-Module –Name MODULE-NAME`    <—從 PowerShell 資源庫抓取模組
- 從 `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` 模組資料夾複製到暫存資料夾
- 刪除主要資料夾中的範例和文件
- 壓縮主要資料夾，ZIP 檔案的命名與資料夾完全相同 
- 將 ZIP 檔案放到可存取的 HTTP 位置，例如 Azure 儲存體帳戶中的 Blob 儲存體。
- 執行此 PowerShell：

  ```powershell
  New-AzAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

包含的範例為 cChoco 和 xNetworking 實現這些步驟。 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>步驟 4: 將節點配置加入到拉取伺服器

第一次將組態匯入到提取伺服器並編譯，並沒有什麼特殊之處。 所有以後導入或編譯相同配置的外觀完全相同。 每次更新封裝且需要向外推送到生產環境時，在確定組態檔正確之後 (包括封裝的新版本)，就會執行此步驟。 組態檔和 PowerShell 如下：

ISVBoxConfig.ps1：

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

新設定文稿.ps1(修改為使用 Az 模組):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

這些步驟導致在拉取伺服器上放置名為"ISVBoxConfig.isvbox"的新節點配置。 節點配置名稱生成為"配置名稱.nodename"。

## <a name="step-5-create-and-maintain-package-metadata"></a>第 5 步:建立與維護套件中繼資料

對於放入封裝儲存機制中的每一個封裝，您需要 nuspec 來描述它。
該 nuspec 必須編譯並儲存在 NuGet 伺服器中。 此程序的說明請參閱 [這裡](https://docs.nuget.org/create/creating-and-publishing-a-package)。 您可以使用 MyGet.org 做為 NuGet 伺服器。 他們銷售這項服務，但有免費的入門 SKU。 在NuGet.org,您將找到有關為私有套件安裝自己的 NuGet 伺服器的說明。

## <a name="step-6-tie-it-all-together"></a>第6步:把一切都綁在一起

每次版本通過 QA 並獲得批准進行部署時,都會創建包,並將 nuspec 和 nupkg 更新並部署到 NuGet 伺服器。 還必須更新配置(上面的步驟 4)以符合新版本號。 然後,必須將其發送到拉取伺服器並進行編譯。

從這裡開始，則須由依存於該組態的 VM 提取更新並加以安裝。 每個更新都很簡單 - 只需一兩行 PowerShell。 對於 Azure DevOps,其中一些可以封裝在生成任務中,這些任務可以連結在一起生成。 [本文](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery)將詳加說明。 此[GitHub 儲存庫](https://github.com/Microsoft/vso-agent-tasks)詳細介紹了可用的生成任務。

## <a name="related-articles"></a>相關文章
* [Azure 自動化 DSC 概觀](automation-dsc-overview.md)
* [Azure 自動化 DSC Cmdlet](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [上架由 Azure 自動化 DSC 管理的機器](automation-dsc-onboarding.md)

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱 [Azure 自動化狀態設定](automation-dsc-overview.md)
- 若要開始使用，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)
- 若要了解如何編譯 DSC 組態，以將它們指派給目標節點，請參閱[編譯 Azure Automation State Configuration 中的組態](automation-dsc-compile.md)
- 如需 PowerShell Cmdlet 參考，請參閱 [Azure 自動化狀態設定 Cmdlet](/powershell/module/azurerm.automation/#automation)
- 如需定價資訊，請參閱 [Azure 自動化狀態設定的定價](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)
