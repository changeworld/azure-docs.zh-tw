---
title: 從現有伺服器創建配置 - Azure 自動化
description: 瞭解如何從 Azure 自動化的現有伺服器創建配置。
keywords: dsc,powershell,設定,安裝
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d888c8fd3bf7cd44c37e7225618bd197f419d275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030136"
---
# <a name="create-configurations-from-existing-servers"></a>從現有伺服器創建配置

> 適用于： Windows 電源外殼 5.1

從現有伺服器創建配置可能是一項具有挑戰性的任務。
您可能不希望*所有*設置，只想要您關心的設置。
即便如此，您也需要知道必須按何種順序應用設置才能成功應用配置。

> [!NOTE]
> 本文引用由開源社區維護的解決方案。
> 支援僅以 GitHub 協作的形式提供，而不是從 Microsoft 獲得。

## <a name="community-project-reversedsc"></a>社區專案：反向DSC

已創建名為[ReverseDSC](https://github.com/microsoft/reversedsc)的社區維護解決方案，以在此區域工作，開始 SharePoint。

該解決方案基於[SharePointDSC 資源](https://github.com/powershell/sharepointdsc)，並將其擴展以協調從現有 SharePoint 伺服器[收集資訊](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use)。
最新版本具有多種[提取模式](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes)，用於確定要包括的資訊級別。

使用該解決方案的結果是生成要與 SharePointDSC 配置腳本一起使用的[配置資料](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data)。

生成資料檔案後，可以使用[DSC 配置腳本](/powershell/scripting/dsc/overview/overview)生成 MOF 檔案並將[MOF 檔案上載到 Azure 自動化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然後從[本地](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[Azure 註冊](/azure/automation/automation-dsc-onboarding#azure-virtual-machines)伺服器以提取配置。

要嘗試反向DSC，請訪問[PowerShell 庫](https://www.powershellgallery.com/packages/ReverseDSC/)並下載解決方案，或按一下"專案網站"查看[文檔](https://github.com/Microsoft/sharepointDSC.reverse)。

## <a name="next-steps"></a>後續步驟

- [Windows PowerShell 預期狀態設定概觀](/powershell/scripting/dsc/overview/overview)
- [DSC 資源](/powershell/scripting/dsc/resources/resources)
- [配置本地組態管理員](/powershell/scripting/dsc/managing-nodes/metaconfig)
