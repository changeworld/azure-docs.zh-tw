---
title: 大規模配置資料 - Azure 自動化
description: 瞭解如何在 Azure 自動化中大規模配置資料以進行狀態配置。
keywords: dsc,powershell,設定,安裝
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1a77a366ee8e06b2d8c47eb3b47eeaf9ae809598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028296"
---
# <a name="configuration-data-at-scale"></a>大規模組態資料

> 適用于： Windows 電源外殼 5.1

管理數百或數千台伺服器可能是一項挑戰。
客戶提供了回饋，最困難的方面是實際管理[配置資料](/powershell/scripting/dsc/configurations/configdata)。
跨邏輯構造（如位置、類型和環境）組織資訊。

> [!NOTE]
> 本文引用由開源社區維護的解決方案。
> 支援僅以 GitHub 協作的形式提供，而不是從 Microsoft 獲得。

## <a name="community-project-datum"></a>社區專案：達圖姆

已創建名為[Datum](https://github.com/gaelcolas/Datum)的社區維護解決方案來解決此挑戰。
基準基於其他建構管理平臺的卓越創意，並為 PowerShell DSC 實現相同類型的解決方案。
資訊根據邏輯想法[組織到文字檔](https://github.com/gaelcolas/Datum#3-intended-usage)。
範例為：

- 應全域應用的設置
- 應應用於位置中的所有伺服器的設置
- 應應用於所有資料庫伺服器的設置
- 單個伺服器設置

此資訊以您喜歡的檔案格式（JSON、Yaml 或 PSD1）組織。
然後提供 Cmdlet，通過將每個檔[的資訊合併](https://github.com/gaelcolas/Datum#datum-tree)到伺服器或伺服器角色的單個視圖來組建組態檔資料檔案。

生成資料檔案後，可以使用[DSC 配置腳本](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)生成 MOF 檔案並將[MOF 檔案上載到 Azure 自動化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然後從[本地](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[Azure 註冊](/azure/automation/automation-dsc-onboarding#azure-virtual-machines)伺服器以提取配置。

要試用基準圖，請訪問[PowerShell 庫](https://www.powershellgallery.com/packages/datum/)並下載解決方案，或按一下"專案網站"查看[文檔](https://github.com/gaelcolas/Datum#2-getting-started--concepts)。

## <a name="next-steps"></a>後續步驟

- [Windows PowerShell 預期狀態設定概觀](/powershell/scripting/dsc/overview/overview)
- [DSC 資源](/powershell/scripting/dsc/resources/resources)
- [配置本地組態管理員](/powershell/scripting/dsc/managing-nodes/metaconfig)
