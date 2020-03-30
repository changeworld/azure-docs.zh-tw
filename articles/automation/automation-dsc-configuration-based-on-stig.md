---
title: 基於 STIG 的配置，用於狀態配置 - Azure 自動化
description: 瞭解 Azure 自動化中基於 STIG 的狀態配置的配置。
keywords: dsc,powershell,設定,安裝
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028270"
---
# <a name="configuration-based-on-stig"></a>以 STIG 為基礎的設定

> 適用于： Windows 電源外殼 5.1

首次創建配置內容可能具有挑戰性。
在許多情況下，目標是按照"基線"自動設定伺服器，該"基線"有望與行業建議保持一致。

> [!NOTE]
> 本文引用由開源社區維護的解決方案。
> 支援僅以 GitHub 協作的形式提供，而不是從 Microsoft 獲得。

## <a name="community-project-powerstig"></a>社區專案：PowerSTIG

名為[PowerSTIG](https://github.com/microsoft/powerstig)的社區專案旨在通過基於有關 STIG[的公共資訊](https://public.cyber.mil/stigs/)（安全技術實施指南）生成 DSC 內容來解決此問題。

處理基線比聽起來要複雜得多。
許多組織需要[記錄規則的異常](https://github.com/microsoft/powerstig#powerstigdata)，並大規模管理該資料。
PowerSTIG 通過提供[複合資源](https://github.com/microsoft/powerstig#powerstigdsc)來解決配置的每個區域，而不是嘗試在一個大檔中解決整個設置範圍，從而解決了問題。

組建組態後，可以使用[DSC 配置腳本](/powershell/scripting/dsc/configurations/configurations)生成 MOF 檔案並將[MOF 檔案上載到 Azure 自動化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然後從[本地](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[Azure 註冊](/azure/automation/automation-dsc-onboarding#azure-virtual-machines)伺服器以提取配置。

要試用 PowerSTIG，請訪問[PowerShell 庫](https://www.powershellgallery.com)並下載解決方案，或按一下"專案網站"查看[文檔](https://github.com/microsoft/powerstig)。

## <a name="next-steps"></a>後續步驟

- [Windows PowerShell 預期狀態設定概觀](/powershell/scripting/dsc/overview/overview)
- [DSC 資源](/powershell/scripting/dsc/resources/resources)
- [配置本地組態管理員](/powershell/scripting/dsc/managing-nodes/metaconfig)
