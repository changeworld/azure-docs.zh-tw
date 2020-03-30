---
title: 將配置轉換為用於狀態配置的複合資源 - Azure 自動化
description: 瞭解如何將配置轉換為 Azure 自動化中狀態配置的複合資源。
keywords: dsc,powershell,設定,安裝
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 05f0a81a738688df15ea9060071d9e266b54b7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136650"
---
# <a name="convert-configurations-to-composite-resources"></a>將設定轉換成複合資源

> 適用于： Windows 電源外殼 5.1

開始創作配置後，可以快速建立管理設置組的"方案"。
範例為：

- 創建 Web 服務器
- 創建 DNS 伺服器
- 創建 SharePoint 伺服器
- 配置 SQL 群集
- 管理防火牆設置
- 管理密碼設置

如果您有興趣與他人共用此工作，則最佳選項是將配置打包為[複合資源](/powershell/scripting/dsc/resources/authoringresourcecomposite)。
首次創建複合資源可能非常難。

> [!NOTE]
> 本文引用由開源社區維護的解決方案。
> 支援僅以 GitHub 協作的形式提供，而不是從 Microsoft 獲得。

## <a name="community-project-compositeresource"></a>社區專案：綜合資源

已創建名為[複合資源](https://github.com/microsoft/compositeresource)的社區維護解決方案，以解決此難題。

複合資源可自動執行從配置創建新模組的過程。
首先，在工作站（或生成伺服器）上[採購](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/)配置腳本，以便將其載入到記憶體中。
接下來，使用複合資源模組提供的功能自動轉換，而不是回合組態以生成 MOF 檔案。
Cmdlet 將載入配置的內容，獲取參數清單，並生成包含所需內容的新模組。

生成模組後，可以在每次進行更改時增加版本並添加版本資訊，並將其發佈到自己的[PowerShellGet 存儲庫](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)。

創建包含配置（或多個配置）的複合資源模組後，可以在 Azure 中的["可組合創作體驗](/azure/automation/compose-configurationwithcompositeresources)"中使用它們，或將它們添加到[DSC 配置腳本](/powershell/scripting/dsc/configurations/configurations)以生成 MOF 檔案並將[MOF 檔案上載到 Azure 自動化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然後從[本地](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[Azure 註冊](/azure/automation/automation-dsc-onboarding#azure-virtual-machines)伺服器以提取配置。
專案的最新更新還發佈了 Azure 自動化[的 Runbook，](https://www.powershellgallery.com/packages?q=DscGallerySamples)以自動執行從 PowerShell 庫導入配置的過程。

要嘗試為 DSC 自動創建複合資源，請訪問[PowerShell 庫](https://www.powershellgallery.com/packages/compositeresource/)並下載解決方案，或按一下"專案網站"查看[文檔](https://github.com/microsoft/compositeresource)。

## <a name="next-steps"></a>後續步驟

- [Windows PowerShell 預期狀態設定概觀](/powershell/scripting/dsc/overview/overview)
- [DSC 資源](/powershell/scripting/dsc/resources/resources)
- [配置本地組態管理員](/powershell/scripting/dsc/managing-nodes/metaconfig)
