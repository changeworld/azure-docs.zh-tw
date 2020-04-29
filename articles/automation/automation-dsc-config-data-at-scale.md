---
title: 大規模設定資料-Azure 自動化
description: 瞭解如何在 Azure 自動化中針對狀態設定進行大規模的資料設定。
keywords: dsc,powershell,設定,安裝
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 644ea1c00af7e71ff56852298fff18e5293c137b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585565"
---
# <a name="configuration-data-at-scale"></a>大規模組態資料

> 適用于： Windows PowerShell 5。1

管理數百部或數千部伺服器可能是一項挑戰。
客戶提供的意見反應是最困難的層面實際上是管理設定[資料](/powershell/scripting/dsc/configurations/configdata)。
跨邏輯結構（例如位置、類型和環境）來組織資訊。

> [!NOTE]
> 本文是指由開放原始碼社區維護的解決方案。
> 支援僅以 GitHub 共同作業的形式提供，而不是來自 Microsoft。

## <a name="community-project-datum"></a>社區專案： Datum

已建立名為[Datum](https://github.com/gaelcolas/Datum)的社區維護解決方案，以解決這項挑戰。
Datum 建基於其他設定管理平臺的絕佳想法，並為 PowerShell DSC 實行相同類型的解決方案。
資訊會根據邏輯概念[組織成文字檔](https://github.com/gaelcolas/Datum#3-intended-usage)。
範例為：

- 應該全域套用的設定
- 應該套用至位置中所有伺服器的設定
- 應套用至所有資料庫伺服器的設定
- 個別伺服器設定

這項資訊會以您偏好的檔案格式（JSON、Yaml 或 .PSD1）來組織。
接著會提供 Cmdlet，藉由將每個檔案中[的資訊合併](https://github.com/gaelcolas/Datum#datum-tree)至伺服器或伺服器角色的單一視圖，來產生設定資料檔案。

一旦產生資料檔案之後，您就可以使用它們來搭配[DSC 設定腳本](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)來產生 mof 檔案，並將[mof 檔案上傳到 Azure 自動化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然後從[內部部署](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[在 Azure 中](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms)註冊您的伺服器，以提取設定。

若要試用 Datum，請造訪[PowerShell 資源庫](https://www.powershellgallery.com/packages/datum/)並下載解決方案，或按一下 [專案網站] 以查看[檔](https://github.com/gaelcolas/Datum#2-getting-started--concepts)。

## <a name="next-steps"></a>後續步驟

- [Windows PowerShell 預期狀態設定概觀](/powershell/scripting/dsc/overview/overview)
- [DSC 資源](/powershell/scripting/dsc/resources/resources)
- [設定本機 Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
