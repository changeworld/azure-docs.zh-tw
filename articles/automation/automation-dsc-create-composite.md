---
title: 將設定轉換成狀態設定的複合資源-Azure 自動化
description: 瞭解如何在 Azure 自動化中將設定轉換成複合資源以進行狀態設定。
keywords: dsc,powershell,設定,安裝
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a39b038d31d1b4a614ff0acf7df2586706bb0404
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585520"
---
# <a name="convert-configurations-to-composite-resources"></a>將設定轉換成複合資源

> 適用于： Windows PowerShell 5。1

開始撰寫設定之後，您可以快速建立「案例」來管理設定群組。
範例為：

- 建立 web 伺服器
- 建立 DNS 伺服器
- 建立 SharePoint 伺服器
- 設定 SQL 叢集
- 管理防火牆設定
- 管理密碼設定

如果您想要與其他人共用這項工作，最佳選項是將設定封裝為[複合資源](/powershell/scripting/dsc/resources/authoringresourcecomposite)。
第一次建立複合資源可能會很困難。

> [!NOTE]
> 本文是指由開放原始碼社區維護的解決方案。
> 支援僅以 GitHub 共同作業的形式提供，而不是來自 Microsoft。

## <a name="community-project-compositeresource"></a>社區專案： CompositeResource

已建立名為[CompositeResource](https://github.com/microsoft/compositeresource)的社區維護解決方案，以解決這項挑戰。

CompositeResource 會自動化從您的設定建立新模組的程式。
您會在工作站（或組建伺服器）上，以[點](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/)為起始設定腳本，使其載入記憶體中。
接下來，您可以使用 CompositeResource 模組所提供的函式來自動化轉換，而不是執行設定來產生 MOF 檔案。
此 Cmdlet 會載入您的設定內容、取得參數的清單，並以您需要的所有專案產生新的模組。

產生模組之後，您可以在每次進行變更時遞增版本並新增版本資訊，並將其發佈至您自己的[PowerShellGet 存放庫](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)。

建立包含設定（或多個設定）的複合資源模組之後，您可以在 Azure 的可組合撰寫[體驗](/azure/automation/compose-configurationwithcompositeresources)中使用它們，或將它們新增至[DSC 設定腳本](/powershell/scripting/dsc/configurations/configurations)來產生 mof 檔案，並將[mof 檔案上傳到 Azure 自動化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然後從[內部部署](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[在 Azure 中](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms)註冊您的伺服器，以提取設定。
專案的最新更新也已發行 Azure 自動化的[runbook](https://www.powershellgallery.com/packages?q=DscGallerySamples) ，可將從 PowerShell 資源庫匯入設定的程式自動化。

若要嘗試自動建立 DSC 的複合資源，請造訪[PowerShell 資源庫](https://www.powershellgallery.com/packages/compositeresource/)並下載解決方案，或按一下 [專案網站] 以查看[檔](https://github.com/microsoft/compositeresource)。

## <a name="next-steps"></a>後續步驟

- [Windows PowerShell 預期狀態設定概觀](/powershell/scripting/dsc/overview/overview)
- [DSC 資源](/powershell/scripting/dsc/resources/resources)
- [設定本機 Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
