---
title: 將設定轉換成 Azure 自動化狀態設定的複合資源
description: 本文說明如何將設定轉換成 Azure 自動化狀態設定的複合資源。
keywords: dsc,powershell,設定,安裝
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b3a49fac5600e6338d5fb56281a3360f0abaa39a
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836969"
---
# <a name="convert-configurations-to-composite-resources"></a>將設定轉換成複合資源

> 適用於：Windows PowerShell 5.1

開始撰寫設定之後，您可以快速建立「案例」來管理設定群組。
範例為：

- 建立網頁伺服器
- 建立 DNS 伺服器
- 建立 SharePoint 伺服器
- 設定 SQL 叢集
- 管理防火牆設定
- 管理密碼設定

如果您想要與其他人共用這項工作，最佳選項是將設定封裝為[複合資源](/powershell/scripting/dsc/resources/authoringresourcecomposite)。
第一次建立複合資源可能會很困難。

> [!NOTE]
> 本文討論由開放原始碼社群所維護的解決方案。
> 支援僅以 GitHub 共同作業的形式提供，而不是來自 Microsoft。

## <a name="community-project-compositeresource"></a>社群專案：CompositeResource

已建立的 [CompositeResource](https://github.com/microsoft/compositeresource) 社群維護解決方案可解決這項挑戰。

CompositeResource 會將從您設定中建立新模組的程序自動化。
您一開始會在工作站 (或組建伺服器) 上，對設定指令碼使用[點執行](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/)，使其載入記憶體中。
接下來，您可以使用 CompositeResource 模組所提供的函式來自動化轉換，而不是執行設定來產生 MOF 檔案。
此 Cmdlet 會載入您的設定內容、取得參數清單，並根據您所有需求產生新的模組。

產生模組之後，您可以在每次進行變更時遞增版本並新增版本資訊，然後將其發佈至您自己的 [PowerShellGet 存放庫](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)。

建立包含設定 (或多個設定) 的複合資源模組之後，您可以在 Azure 中的[可組合撰寫體驗](/azure/automation/compose-configurationwithcompositeresources)中使用這些模組，或將其新增至 [DSC 設定指令碼](/powershell/scripting/dsc/configurations/configurations)來產生 MOF 檔案，然後[將 MOF 檔案上傳到 Azure 自動化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然後從[內部部署](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[在 Azure 中](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms)註冊您的伺服器，以提取設定。
專案的最新更新也已發行 [unbook](https://www.powershellgallery.com/packages?q=DscGallerySamples)，Azure 自動化可將從 PowerShell 資源庫匯入設定的程序自動化。

若要嘗試自動建立 DSC 的複合資源，請造訪 [PowerShell 資源庫](https://www.powershellgallery.com/packages/compositeresource/)並下載解決方案，或按一下 [專案網站] 以檢視[文件](https://github.com/microsoft/compositeresource)。

## <a name="next-steps"></a>後續步驟

- 若要了解 PowerShell DSC，請參閱 [Windows PowerShell Desired State Configuration 概觀](/powershell/scripting/dsc/overview/overview)。
- 探索 [DSC 資源](/powershell/scripting/dsc/resources/resources)中的 PowerShell DSC 資源。
- 如需本機設定管理員設定的詳細資訊，請參閱[設定本機設定管理員](/powershell/scripting/dsc/managing-nodes/metaconfig)。
