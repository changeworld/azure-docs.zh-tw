---
title: 從現有伺服器建立設定 - Azure 自動化
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
ms.openlocfilehash: dff9b8f52207a38cf7eaddefa178aff262ddc546
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585556"
---
# <a name="create-configurations-from-existing-servers"></a>從現有伺服器建立設定

> 適用於: Windows 電源外殼 5.1

從現有伺服器創建配置可能是一項具有挑戰性的任務。
您可能不希望*所有*設置,只想要您關心的設置。
即便如此,您也需要知道必須按何種順序應用設置才能成功應用配置。

> [!NOTE]
> 本文引用由開源社區維護的解決方案。
> 支援僅以 GitHub 協作的形式提供,而不是從 Microsoft 獲得。

## <a name="community-project-reversedsc"></a>社區專案:反向DSC

已創建名為[ReverseDSC](https://github.com/microsoft/reversedsc)的社區維護解決方案,以在此區域工作,開始 SharePoint。

該解決方案基於[SharePointDSC 資源](https://github.com/powershell/sharepointdsc),並將其擴展以協調從現有 SharePoint 伺服器[收集資訊](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use)。
最新版本具有多種[提取模式](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes),用於確定要包括的資訊級別。

使用該解決方案的結果是產生要與 SharePointDSC 設定文稿一起使用的[設定資料](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data)。

產生資料檔後,可以使用[DSC 設定文稿](/powershell/scripting/dsc/overview/overview)產生 MOF 檔並將[MOF 檔上載到 Azure 自動化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然後從[本地](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[Azure 註冊](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms)伺服器以提取配置。

要嘗試反向DSC,請造[訪 PowerShell 函式庫](https://www.powershellgallery.com/packages/ReverseDSC/)並下載解決方案,或按下「專案網站」檢視[文件](https://github.com/Microsoft/sharepointDSC.reverse)。

## <a name="next-steps"></a>後續步驟

- [Windows PowerShell 預期狀態設定概觀](/powershell/scripting/dsc/overview/overview)
- [DSC 資源](/powershell/scripting/dsc/resources/resources)
- [設定本地端組態管理員](/powershell/scripting/dsc/managing-nodes/metaconfig)
