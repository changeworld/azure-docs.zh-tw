---
title: 使用 Chocolatey 狀態設定持續部署 Azure 自動化
description: 說明如何搭配 Chocolatey 套件管理員使用 Azure 自動化狀態設定來進行 DevOps 持續部署。 包含包含完整 JSON Resource Manager 範本和 PowerShell 來源的範例。
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 278c6ee05fdf78cbfa8653381b65233fbb513593
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996112"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>使用自動化狀態設定和 Chocolatey，為虛擬機器提供持續部署

在 DevOps 的世界中，有許多工具可協助持續整合管線中的各個點。 Azure 自動化[狀態設定](automation-dsc-overview.md)是 DevOps 團隊可以採用的新選項的歡迎加入。 

Azure 自動化是 Microsoft Azure 中的受控服務，可讓您使用 runbook、節點和共用資源（例如認證、排程和全域變數）來自動化各種工作。 Azure 自動化狀態設定延伸這項自動化功能，以包含 PowerShell Desired State Configuration （DSC）工具。 以下提供清楚的[概觀](automation-dsc-overview.md)。

本文示範如何設定 Windows 電腦的持續部署（CD）。 您可以輕鬆地擴充技術，以在角色中包含所需的 Windows 電腦（例如網站），並從該處移至其他角色。

![IaaS VM 的持續部署](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>概括而言

這裡有很多，但幸運的是，它可以分成兩個主要程式：

- 撰寫程式碼並測試，然後針對系統的主要和次要版本，建立並發佈安裝封裝。
- 建立和管理在套件中安裝和執行程式碼的 Vm。  

一旦這兩個核心程式都已準備就緒，就可以在建立和部署新版本時，輕鬆地在您的 Vm 上自動更新套件。

## <a name="component-overview"></a>元件概觀

套件管理員（例如[apt](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) ）在 Linux 世界中很知名，但在 Windows 世界中並沒有太大的説明。
[Chocolatey](https://chocolatey.org/)就是這種事，而 Scott Hanselman 的[文章](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx)是一項絕佳的介紹。 簡言之，Chocolatey 可讓您使用命令列，將套件從中央存放庫安裝到 Windows 作業系統上。 您可以建立和管理您自己的儲存機制，而 Chocolatey 可以從您指定的任何數量的儲存機制來安裝封裝。

[POWERSHELL DSC](/powershell/scripting/dsc/overview/overview)是一個 powershell 工具，可讓您宣告電腦所需的設定。 例如，如果您想要安裝 Chocolatey、已安裝 IIS、已開啟埠80，以及已安裝1.0.0 版的網站，DSC 本機 Configuration Manager （LCM）會執行該設定。 DSC 提取伺服器會保存電腦設定的儲存機制。 每台電腦上的 LCM 會定期檢查電腦的組態是否符合已儲存的組態。 它可能報告狀態，也可能嘗試讓電腦回復到符合已儲存的組態。 您可以編輯提取伺服器上儲存的組態，讓一台電腦或一群電腦符合已變更的組態。

DSC 資源是具有特定功能的程式碼模組，例如管理網路、Active Directory 或 SQL Server。 Chocolatey DSC 資源知道如何存取 NuGet 伺服器 (還有其他)、下載封裝、安裝封裝...等等。 [PowerShell 資源庫](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)中有許多其他 DSC 資源。 您會在 Azure 自動化狀態設定提取伺服器上安裝這些模組，以供您的設定使用。

Resource Manager 範本提供宣告式方式來產生基礎結構，例如網路、子網、網路安全性和路由、負載平衡器、Nic、Vm 等等。 以下是將 Resource Manager 部署模型（宣告）與 Azure 服務管理（ASM 或傳統）部署模型（命令式）進行比較的[文章](../azure-resource-manager/management/deployment-models.md)。 本文包含核心資源提供者的討論：計算、儲存體和網路。

Resource Manager 範本的主要功能之一，就是能夠在布建時將 VM 延伸模組安裝到 VM。 VM 延伸模組具有特定功能，例如執行自訂腳本、安裝防毒軟體，以及執行 DSC 設定腳本。 有許多其他類型的 VM 延伸模組。

## <a name="quick-trip-around-the-diagram"></a>圖表速覽

從頂端開始，您可以撰寫程式碼、加以建立、測試，然後建立安裝套件。 Chocolatey 可以處理各種類型的安裝封裝，例如 MSI、MSU、ZIP 等。 而且，如果 Chocolatey 的原生功能不在其上，您就有 PowerShell 的完整功能可執行實際安裝。 將封裝放入某個可連線的位置–套件存放庫。 這個使用範例使用 Azure blob 儲存體帳戶中的公用資料夾，但可以是任何位置。 Chocolatey 可自然搭配 NuGet 伺服器和其他一些工具，一起管理封裝中繼資料。 [這篇文章](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) 說明選項。 使用範例會使用 NuGet。 Nuspec 是封裝的中繼資料。 Nuspec 資訊會編譯成 NuPkg 並儲存在 NuGet 伺服器上。 當您的設定依名稱要求套件並參照 NuGet 伺服器時，VM 上的 Chocolatey DSC 資源會抓取套件並加以安裝。 您也可以要求特定版本的封裝。

在圖片的左下方有一個 Azure Resource Manager 範本。 在此使用範例中，VM 擴充功能會以 Azure 自動化狀態設定提取伺服器，將 VM 註冊為節點。 設定會儲存在提取伺服器兩次：一次是純文字，一次編譯為 MOF 檔案。 在 Azure 入口網站中，MOF 代表節點設定，而不是簡單的設定。 這是與節點相關聯的成品，因此節點會知道其設定。 下列詳細資料示範如何將節點組態指派給節點。

建立 Nuspec、編譯它，並將它儲存在 NuGet 伺服器中，是一件很小的事。 您已開始管理 VM。 

進行持續部署的下一個步驟需要設定提取伺服器一次，一次註冊您的節點，並在伺服器上建立和儲存初始設定。 當套件升級並部署至存放庫時，您只需要在必要時重新整理提取伺服器上的設定和節點設定。

如果您不是從 Resource Manager 範本開始，這沒什麼問題。 有 PowerShell 命令可協助您向提取伺服器註冊您的 Vm。 如需詳細資訊，請參閱將[機器上架以進行管理 Azure 自動化狀態設定](automation-dsc-onboarding.md)。

## <a name="about-the-usage-example"></a>關於使用範例

本文中的使用範例會從 Azure 資源庫中的一般 Windows Server 2012 R2 映射的 VM 開始。 您可以從任何預存的映像開始，再從那裡使用 DSC 組態進行調整。
不過，變更已併入影像的組態，要比使用 DSC 動態更新組態難得多。

將這項技巧運用在 VM 時，不需要使用 Resource Manager 範本和 VM 擴充功能。 此外，VM 不在 Azure 上也能由 CD 管理。 只需要在 VM 上安裝 Chocolatey，並設定 LCM 使其得知提取伺服器所在位置即可。

當您更新生產環境中 VM 上的套件時，您必須在安裝更新時，讓該 VM 離開輪替。 視情況而定，作法會有很大的差異。 例如，如果 VM 在 Azure 負載平衡器後方，您可以加入自訂探查。 更新 VM 時，讓探查端點傳回 400。 可在組態中進行必要調整來引起此變更，但更新完成時，此調整會恢復成傳回 200。

此使用範例的完整原始檔位於 GitHub 上的 [Visual Studio 專案](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) 。

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>步驟1：設定提取伺服器和自動化帳戶

在已驗證的 (`Connect-AzAccount`) PowerShell 命令列上：(設定提取伺服器可能需要幾分鐘的時間)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

您可以將您的自動化帳戶放入下列任何區域（也稱為「位置」）：美國東部2、美國中南部、US Gov 維吉尼亞州、西歐、東南亞、日本東部、印度中部和澳大利亞東南部、加拿大中部、北歐。

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>步驟2：對 Resource Manager 範本進行 VM 擴充功能調整

此 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)提供 VM 註冊的詳細資料 (使用 PowerShell DSC VM 延伸模組)。
此步驟會將新的 VM 註冊到提取伺服器的狀態設定節點清單中。 這項註冊的一部分會指定要套用至節點的節點組態。 此節點設定不一定要存在於提取伺服器中，因此，步驟4就是第一次完成這項作業。 但在步驟 2 中，您必須決定節點名稱和組態名稱。 在此使用範例中，節點名稱是 'isvbox'，組態名稱是 'ISVBoxConfig'。 因此，節點組態名稱 (將在 DeploymentTemplate.json 中指定) 是 'ISVBoxConfig.isvbox'。

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>步驟3：將所需的 DSC 資源加入到提取伺服器

PowerShell 資源庫會自動將 DSC 資源安裝到您的 Azure 自動化帳戶。
流覽至您想要的資源，然後按一下 [部署至 Azure 自動化] 按鈕。

![PowerShell 資源庫範例](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

最近新增至 Azure 入口網站的另一項技術可讓您提取新的模組，或更新現有的模組。 按一下 [自動化帳戶] 資源、[資產] 圖格，最後是 [模組] 圖格。 [流覽資源庫] 圖示可讓您查看資源庫中的模組清單、向下切入至 [詳細資料]，最後再匯入至您的自動化帳戶。 這是讓您的模組隨時保持最新狀態的絕佳方法。 而且，匯入功能會檢查與其他模組的相依性以確保所有模組都保持同步。

另外還有一種手動方法，除非您稍後想要升級，否則每個資源只會使用一次。 如需有關撰寫 PowerShell 整合模組的詳細資訊，請參閱[撰寫 Azure 自動化的整合模組](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)。

>[!NOTE]
>Windows 電腦的 PowerShell 整合模組的資料夾結構與 Azure 自動化所預期的資料夾結構稍有不同。 

1. 安裝[Windows Management Framework v5](https://aka.ms/wmf5latest) （windows 10 不需要）。

2. 安裝整合模組。

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. 將模組資料夾從**C:\Program Files\WindowsPowerShell\Modules\MODULE-NAME**複製到暫存資料夾。

4. 從主資料夾中刪除範例和檔。

5. 壓縮主要資料夾，使用資料夾名稱將 ZIP 檔案命名為。

6. 將 ZIP 檔案放入可連線的 HTTP 位置，例如 Azure 儲存體帳戶中的 blob 儲存體。

7. 執行下列命令。

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

包含的範例會針對瞭解 cchoco 方式和 xNetworking 執行這些步驟。 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>步驟4：將節點設定新增至提取伺服器

第一次將組態匯入到提取伺服器並編譯，並沒有什麼特殊之處。 之後所有的相同設定匯入或編譯看起來完全相同。 每次更新封裝且需要向外推送到生產環境時，在確定組態檔正確之後 (包括封裝的新版本)，就會執行此步驟。 以下是設定檔**isvboxconfig.isvbox**：

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

以下是**New-ConfigurationScript**腳本（已修改為使用 Az 模組）：

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

這些步驟會導致將名為**isvboxconfig.isvbox**的新節點設定放在提取伺服器上。 節點設定名稱的建立方式為`configurationName.nodeName`。

## <a name="step-5-create-and-maintain-package-metadata"></a>步驟5：建立和維護封裝中繼資料

對於您放入封裝存放庫中的每個套件，您需要一個描述它的 Nuspec。 它必須經過編譯並儲存在您的 NuGet 伺服器上。 此程序的說明請參閱 [這裡](https://docs.nuget.org/create/creating-and-publishing-a-package)。 

您可以使用**MyGet.org**做為 NuGet 伺服器。 您可以購買此服務，但三個是免費的入門 SKU。 在[NuGet](https://www.nuget.org/)中，您可以找到為私人套件安裝自己的 NuGet 伺服器的指示。

## <a name="step-6-tie-it-all-together"></a>步驟6：將它全部結合

每次版本通過 QA 並核准部署時，就會建立套件，並將 nuspec 和 nupkg 更新並部署至 NuGet 伺服器。 您也必須更新設定（步驟4）以同意新版本號碼。 接著，必須將它傳送至提取伺服器並進行編譯。

從這裡開始，則須由依存於該組態的 VM 提取更新並加以安裝。 每一個更新都很簡單，只需要一行或兩個 PowerShell。 針對 Azure DevOps，其中有些元件會封裝在組建工作中，這些專案可以在組建中連結在一起。 [本文](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery)將詳加說明。 此[GitHub](https://github.com/Microsoft/vso-agent-tasks)存放庫會詳細說明可用的組建工作。

## <a name="related-articles"></a>相關文章
* [Azure 自動化 DSC 總覽](automation-dsc-overview.md)
* [上架由 Azure 自動化 DSC 管理的機器](automation-dsc-onboarding.md)

## <a name="next-steps"></a>後續步驟

- 如需總覽，請參閱[Azure 自動化狀態設定](automation-dsc-overview.md)。
- 若要開始使用，請參閱[Azure 自動化狀態設定](automation-dsc-getting-started.md)使用者入門。
- 若要瞭解如何編譯 DSC 設定，讓您可以將它們指派給目標節點，請參閱[在 Azure 自動化狀態設定中編譯](automation-dsc-compile.md)設定。
- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 如需定價資訊，請參閱[Azure 自動化狀態設定定價](https://azure.microsoft.com/pricing/details/automation/)。
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)。
