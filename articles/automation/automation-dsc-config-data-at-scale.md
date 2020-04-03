---
title: 大規模設定資料 - Azure 自動化
description: 瞭解如何在 Azure 自動化中大規模配置數據以進行狀態配置。
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
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585565"
---
# <a name="configuration-data-at-scale"></a>大規模組態資料

> 適用於: Windows 電源外殼 5.1

管理數百或數千台伺服器可能是一項挑戰。
客戶提供了回饋,最困難的方面是實際管理[設定資料](/powershell/scripting/dsc/configurations/configdata)。
跨邏輯構造(如位置、類型和環境)組織資訊。

> [!NOTE]
> 本文引用由開源社區維護的解決方案。
> 支援僅以 GitHub 協作的形式提供,而不是從 Microsoft 獲得。

## <a name="community-project-datum"></a>社區專案:達圖姆

已創建名為[Datum](https://github.com/gaelcolas/Datum)的社區維護解決方案來解決此挑戰。
基準基於其他配置管理平臺的卓越創意,並為 PowerShell DSC 實現相同類型的解決方案。
資訊根據邏輯想法[組織到文字檔](https://github.com/gaelcolas/Datum#3-intended-usage)。
範例為：

- 應全域應用的設定
- 應套用到位置中的所有伺服器的設定
- 應套用所有資料庫伺服器的設定
- 單個伺服器設定

此資訊以您喜歡的檔案格式(JSON、Yaml 或 PSD1)組織。
然後提供 cmdlet,透過每個檔案[的資訊合併](https://github.com/gaelcolas/Datum#datum-tree)到伺服器或伺服器角色的單個檢視來生成設定檔資料檔。

產生資料檔後,可以使用[DSC 設定文稿](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)產生 MOF 檔並將[MOF 檔上載到 Azure 自動化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然後從[本地](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[Azure 註冊](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms)伺服器以提取配置。

要試用基礎圖,請造[訪 PowerShell 函式庫](https://www.powershellgallery.com/packages/datum/)並下載解決方案,或單擊「專案網站」檢視[文件](https://github.com/gaelcolas/Datum#2-getting-started--concepts)。

## <a name="next-steps"></a>後續步驟

- [Windows PowerShell 預期狀態設定概觀](/powershell/scripting/dsc/overview/overview)
- [DSC 資源](/powershell/scripting/dsc/resources/resources)
- [設定本地端組態管理員](/powershell/scripting/dsc/managing-nodes/metaconfig)
