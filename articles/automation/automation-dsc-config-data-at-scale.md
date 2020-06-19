---
title: 大規模設定 Azure 自動化狀態設定的資料
description: 本文說明如何大規模設定 Azure 自動化狀態設定的資料。
keywords: dsc,powershell,設定,安裝
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fbd721bc54ce2de6b7923b0f582c53ffc7da1a73
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837020"
---
# <a name="configure-data-at-scale-for-azure-automation-state-configuration"></a>大規模設定 Azure 自動化狀態設定的資料

> 適用於：Windows PowerShell 5.1

管理數百部或數千部伺服器可能是一大挑戰。
客戶所提供的意見反應顯示最困難的層面是實際管理[設定資料](/powershell/scripting/dsc/configurations/configdata)。
跨邏輯結構 (例如位置、類型和環境) 來組織資訊。

> [!NOTE]
> 本文討論由開放原始碼社群所維護的解決方案。
> 支援僅以 GitHub 共同作業的形式提供，而不是來自 Microsoft。

## <a name="community-project-datum"></a>社群專案：Datum

已建立名為 [Datum](https://github.com/gaelcolas/Datum) 的社群維護解決方案來解決這項挑戰。
Datum 是以其他設定管理平台的絕佳想法為基礎，並針對 PowerShell DSC 實作相同類型的解決方案。
資訊是根據邏輯概念而[組織成文字檔](https://github.com/gaelcolas/Datum#3-intended-usage)。
範例為：

- 應全域套用的設定
- 應套用至位置中所有伺服器的設定
- 應套用至所有資料庫伺服器的設定
- 個別伺服器設定

此資訊會以您偏好的檔案格式 (JSON、Yaml 或 PSD1) 進行組織。
接著會提供 Cmdlet 來產生設定資料檔案，其做法是將每個檔案中的[資訊合併](https://github.com/gaelcolas/Datum#datum-tree)到伺服器或伺服器角色的單一檢視中。

產生資料檔案後，您就可以將其與 [DSC 設定指令碼](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)搭配使用來產生 MOF 檔案，並[將 MOF 檔案上傳到 Azure 自動化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然後從[內部部署](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[在 Azure 中](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms)註冊您的伺服器，以提取設定。

若要試用 Datum，請造訪 [PowerShell 資源庫](https://www.powershellgallery.com/packages/datum/)並下載解決方案，或按一下 [專案網站] 以檢視[文件](https://github.com/gaelcolas/Datum#2-getting-started--concepts)。

## <a name="next-steps"></a>後續步驟

- 若要了解 PowerShell DSC，請參閱 [Windows PowerShell Desired State Configuration 概觀](/powershell/scripting/dsc/overview/overview)。
- 探索 [DSC 資源](/powershell/scripting/dsc/resources/resources)中的 PowerShell DSC 資源。
- 如需本機設定管理員設定的詳細資訊，請參閱[設定本機設定管理員](/powershell/scripting/dsc/managing-nodes/metaconfig)。
