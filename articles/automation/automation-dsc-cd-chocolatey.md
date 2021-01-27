---
title: 設定 Azure 自動化持續部署與 Chocolatey
description: 本文說明如何使用狀態設定和 Chocolatey 套件管理員來設定持續部署。
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: bb5f7b5e8214bd3b04bd7b9544ab4bc589f6c4bf
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896320"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>使用 Chocolatey 設定持續部署

DevOps 領域中，有許多工具可協助處理持續整合管線中的各個點。 Azure 自動化[狀態設定](automation-dsc-overview.md)是 DevOps 團隊可以採用的新選項。 

Azure 自動化是 Microsoft Azure 中的受控服務，可讓您使用 Runbook、節點以及共用資源 (例如認證、排程和全域變數)，將各種工作自動化。 Azure 自動化狀態設定擴充了此自動化功能，而包含 PowerShell Desired State Configuration (DSC) 工具。 以下提供清楚的[概觀](automation-dsc-overview.md)。

本文示範如何為 Windows 電腦設定持續部署 (CD)。 您可以輕鬆地擴充技術，視需要在角色 (例如網站) 中包含更多 Windows 電腦，並從該角色再延伸至其他角色。

![IaaS VM 的持續部署](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>概括而言

這裡要進行的事項很多，不過幸好，大致上可分成兩個主要程序：

- 撰寫程式碼並測試，然後針對系統的主要和次要版本，建立並發佈安裝封裝。
- 建立和管理 VM，以安裝及執行封裝中的程式碼。  

完成這兩個核心程序後，隨著建立及部署新版本，可輕鬆自動更新 VM 上的套件。

## <a name="component-overview"></a>元件概觀

[apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) 這類封裝管理員在 Linux 領域中是眾所周知的，但在 Windows 領域中沒有那麼出名。
[Chocolatey](https://chocolatey.org/) 就是如此，Scott Hanselman 的[部落格](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx)對此主題有深入介紹。 簡單的說，Chocolatey 可讓您使用命令列，從中央儲存機制將封裝安裝到 Windows 作業系統。 您可以建立和管理您自己的儲存機制，而 Chocolatey 可以從您指定的任何數量的儲存機制來安裝封裝。

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) 是一個 PowerShell 工具，可讓您為電腦宣告您想要的組態。 例如，如果您想要安裝 Chocolatey、安裝 IIS、開啟連接埠 80，以及安裝 1.0.0 版的網站，DSC 本機 Configuration Manager (LCM) 會實作該組態。 DSC 提取伺服器有一個儲存機制保存您電腦的組態。 每台電腦上的 LCM 會定期檢查電腦的組態是否符合已儲存的組態。 它可能報告狀態，也可能嘗試讓電腦回復到符合已儲存的組態。 您可以編輯提取伺服器上儲存的組態，讓一台電腦或一群電腦符合已變更的組態。

DSC 資源是具有特定功能的程式碼模組，例如管理網路、Active Directory 或 SQL Server。 Chocolatey DSC 資源知道如何存取 NuGet 伺服器 (還有其他)、下載封裝、安裝封裝...等等。 [PowerShell 資源庫](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)中有許多其他 DSC 資源。 您要將這些模組安裝到您的 Azure 自動化狀態設定提取伺服器 (由您安裝)，以供您的組態使用。

Resource Manager 範本以宣告方式產生基礎結構，例如網路、子網路、網路安全性和路由、負載平衡器、NIC、VM 等等。 這篇[文章](../azure-resource-manager/management/deployment-models.md)比較 Resource Manager 部署模型 (宣告) 與 Azure 服務管理 (ASM 或傳統) 部署模型 (必要)。 本文包含核心資源提供者：計算、儲存體和網路的討論。

Resource Manager 範本的主要功能之一，是能夠在佈建時將 VM 延伸模組安裝至 VM 中。 VM 延伸模組具有特定功能，例如執行自訂指令碼、安裝防毒軟體以及執行 DSC 組態指令碼。 有許多其他類型的 VM 延伸模組。

## <a name="quick-trip-around-the-diagram"></a>圖表速覽

從頂端開始，您可以撰寫程式碼、建置及測試，然後建立安裝封裝。 Chocolatey 可以處理各種類型的安裝封裝，例如 MSI、MSU、ZIP 等。 如果 Chocolatey 的原生功能不足夠，您還有 PowerShell 的完整功能可執行實際安裝。 將封裝放入隨手可得之處 – 封裝儲存機制。 這個使用範例使用 Azure blob 儲存體帳戶中的公用資料夾，但可以是任何位置。 Chocolatey 可自然搭配 NuGet 伺服器和其他一些工具，一起管理封裝中繼資料。 [這篇文章](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) 說明選項。 使用範例會使用 NuGet。 Nuspec 是封裝的中繼資料。 Nuspec 資訊會編譯成 NuPkg 並儲存在 NuGet 伺服器上。 當您的組態依名稱要求某個封裝，並參考 NuGet 伺服器時，Chocolatey DSC 資源 (位於 VM 上) 會為您抓取封裝並加以安裝。 您也可以要求特定版本的封裝。

圖表的左下方有一個 Azure Resource Manager 範本。 在這個使用範例中，VM 延伸模組會將 VM 註冊到 Azure 自動化狀態設定提取伺服器以做為節點。 設定會儲存在提取伺服器兩次：一次是純文字，一次則編譯為 MOF 檔案。 在 Azure 入口網站中，MOF 代表節點組態 (而非只是組態)。 其是與節點相關聯的構件，因此節點會知道其組態。 下列詳細資料示範如何將節點組態指派給節點。

建立 Nuspec、編譯並儲存在 NuGet 伺服器中很簡單。 您已開始管理 VM。 

持續部署的下一步需要設定提取伺服器一次、向其註冊節點一次，然後建立初始組態並儲存在伺服器上。 當封裝升級並部署至儲存機制時，您只須視需要重新整理組態及提取伺服器上的節點組態。

如果您不是從 Resource Manager 範本開始，也沒關係。 有一些 PowerShell 命令可協助您向提取伺服器註冊 VM。 如需詳細資訊，請參閱[將機器上架交由 Azure 自動化狀態設定進行管理](automation-dsc-onboarding.md)。

## <a name="about-the-usage-example"></a>關於使用範例

本文中的使用範例開頭 VM 是來自於 Azure 資源庫的一般 Windows Server 2012 R2 映像。 您可以從任何預存的映像開始，再從那裡使用 DSC 組態進行調整。
不過，變更已併入影像的組態，要比使用 DSC 動態更新組態難得多。

將這項技巧運用在 VM 時，不需要使用 Resource Manager 範本和 VM 擴充功能。 此外，VM 不在 Azure 上也能由 CD 管理。 只需要在 VM 上安裝 Chocolatey，並設定 LCM 使其得知提取伺服器所在位置即可。

在生產環境中的 VM 上更新套件時，VM 在安裝更新時必須脫離輪替。 視情況而定，作法會有很大的差異。 例如，如果 VM 在 Azure 負載平衡器後方，您可以加入自訂探查。 更新 VM 時，讓探查端點傳回 400。 可在組態中進行必要調整來引起此變更，但更新完成時，此調整會恢復成傳回 200。

此使用範例的完整原始檔位於 GitHub 上的 [Visual Studio 專案](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) 。

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>步驟 1:設定提取伺服器及自動化帳戶

在已驗證的 (`Connect-AzAccount`) PowerShell 命令列上：(設定提取伺服器可能需要幾分鐘的時間)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

您可以將自動化帳戶放入下列任何區域 (也就是「位置」)︰美國東部 2、美國中南部、US Gov 維吉尼亞州、西歐、東南亞、日本東部、印度中部和澳大利亞東南部、加拿大中部、北歐。

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>步驟 2:VM 延伸模組根據 Resource Manager 範本進行調整

此 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)提供 VM 註冊的詳細資料 (使用 PowerShell DSC VM 延伸模組)。
此步驟會將新的 VM 註冊到提取伺服器的狀態設定節點清單中。 這項註冊的一部分會指定要套用至節點的節點組態。 此節點組態尚無須存在於提取伺服器中，因此這個動作可以在步驟 4 中首次執行。 但在步驟 2 中，您必須決定節點名稱和組態名稱。 在此使用範例中，節點名稱是 'isvbox'，組態名稱是 'ISVBoxConfig'。 因此，節點組態名稱 (將在 DeploymentTemplate.json 中指定) 是 'ISVBoxConfig.isvbox'。

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>步驟 3：將所需的 DSC 資源新增至提取伺服器

PowerShell 資源庫會自動將 DSC 資源安裝到您的 Azure 自動化帳戶。
瀏覽至您需要的資源，然後按一下 [部署到 Azure 自動化] 按鈕。

![PowerShell 資源庫範例](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

最近新增至 Azure 入口網站的另一種技術可讓您提取新的模組或更新現有模組。 按一下 [自動化帳戶資源]、[資產] 圖格和 [模組] 圖格。 [瀏覽資源庫] 圖示可讓您查看資源庫中的模組清單，深入了解詳細資料，並最終匯入您的自動化帳戶。 這是讓您的模組隨時保持最新狀態的絕佳方法。 而且，匯入功能會檢查與其他模組的相依性以確保所有模組都保持同步。

另外還有一種手動方法，除非您稍後想要升級，否則每個資源只會使用一次。 如需關於撰寫 PowerShell 整合模組的詳細資訊，請參閱[撰寫 Azure 自動化的整合模組](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)。

>[!NOTE]
>適用於 Windows 電腦的 PowerShell 整合模組資料夾結構，與 Azure 自動化所需的資料夾結構稍有不同。 

1. 安裝 [Windows Management Framework v5](https://aka.ms/wmf5latest) (Windows 10 不需安裝)。

2. 安裝整合模組。

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. 將模組資料夾從 **c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** 複製到暫存資料夾。

4. 刪除主要資料夾中的範例和文件。

5. 壓縮主要資料夾，ZIP 檔案的命名與資料夾相同。

6. 將 ZIP 檔案放到可存取的 HTTP 位置，例如 Azure 儲存體帳戶中的 Blob 儲存體。

7. 執行下列命令。

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

隨附的範例會針對 cChoco 和 xNetworking 實作這些步驟。 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>步驟 4：將節點組態新增至提取伺服器

第一次將組態匯入到提取伺服器並編譯，並沒有什麼特殊之處。 稍後所有的匯入或編譯相同組態過程完全相同。 每次更新封裝且需要向外推送到生產環境時，在確定組態檔正確之後 (包括封裝的新版本)，就會執行此步驟。 以下是 **ISVBoxConfig.ps1** 的組態檔：

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

以下是 **New-ConfigurationScript.ps1** 指令碼 (已修改為使用 Az 模組)：

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

這些步驟會產生要放在提取伺服器上的新節點組態，名為 **ISVBoxConfig.isvbox**。 節點組態名稱會建置為 `configurationName.nodeName`。

## <a name="step-5-create-and-maintain-package-metadata"></a>步驟 5：建立及維護封裝中繼資料

對於放入封裝儲存機制中的每一個封裝，您需要 Nuspec 來加以描述。 其必須經過編譯並儲存在 NuGet 伺服器中。 此程序的說明請參閱 [這裡](https://docs.nuget.org/create/creating-and-publishing-a-package)。 

您可以使用 **MyGet.org** 做為 NuGet 伺服器。 您可以購買此服務，但有免費的入門 SKU。 您可以在 [NuGet](https://www.nuget.org/) 中，找到針對私人套件自行安裝 NuGet 伺服器的指示。

## <a name="step-6-tie-it-all-together"></a>步驟 6：結合在一起

每當有某個版本通過 QA 並核准可部署時，即會建立封裝，且 nuspec 及 nupkg 會更新並部署至 NuGet 伺服器。 同時也必須更新組態 (步驟 4)，以符合新的版本號碼。 接著，必須將其傳送至提取伺服器並進行編譯。

從這裡開始，則須由依存於該組態的 VM 提取更新並加以安裝。 這些更新內容每一個都很簡單 - 只有一兩行 PowerShell。 針對 Azure DevOps，有些更新會封裝在可一起鏈結在組建內的建置工作中。 [本文](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery)將詳加說明。 此 [GitHub 儲存機制](https://github.com/Microsoft/vso-agent-tasks)會詳細說明可用的組建工作。

## <a name="related-articles"></a>相關文章
* [Azure 自動化 DSC 概觀](automation-dsc-overview.md)
* [將機器上架交由 Azure 自動化 DSC 管理](automation-dsc-onboarding.md)

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱 [Azure 自動化狀態設定概觀](automation-dsc-overview.md)。
- 若要開始使用功能，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)。
- 若要了解如何編譯 DSC 組態，以將其指派給目標節點，請參閱[編譯 Azure 自動化狀態設定中的 DSC 組態](automation-dsc-compile.md)。
- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation)。
- 如需定價資訊，請參閱 [Azure 自動化狀態設定的定價](https://azure.microsoft.com/pricing/details/automation/)。
