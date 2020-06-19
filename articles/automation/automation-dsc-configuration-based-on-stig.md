---
title: 根據 STIG 設定用於 Azure 自動化狀態設定的資料
description: 本文說明如何根據 STIG 設定用於 Azure 自動化狀態設定的資料。
keywords: dsc,powershell,設定,安裝
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d1b05f9e77d3530f3e883aa3f9d98de09c8f54c2
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836986"
---
# <a name="configure-data-based-on-stig"></a>根據 STIG 設定資料

> 適用於：Windows PowerShell 5.1

第一次建立設定內容可能是一項挑戰。
在許多情況下，您的目標是要根據「基準」來自動設定伺服器，以符合業界建議。

> [!NOTE]
> 本文討論由開放原始碼社群所維護的解決方案。
> 支援僅以 GitHub 共同作業的形式提供，而不是來自 Microsoft。

## <a name="community-project-powerstig"></a>社群專案：PowerSTIG

[PowerSTIG](https://github.com/microsoft/powerstig) 是一個社群專案，旨在根據 STIG 的相關[公用](https://public.cyber.mil/stigs/)資訊來產生 DSC 內容 (安全性技術實施指南)，藉以解決此問題。

「基準」的處理其實相當複雜。
許多組織都需要在規則中[記錄例外狀況](https://github.com/microsoft/powerstig#powerstigdata)，並且大規模地管理該資料。
PowerSTIG 解決此問題的方式是，提供[複合資源](https://github.com/microsoft/powerstig#powerstigdsc)解決設定的每個區域，而不是嘗試在一個大型檔案中處理整個範圍的設定。

產生設定後，您就可以使用 [DSC 設定指令碼](/powershell/scripting/dsc/configurations/configurations)來產生 MOF 檔案，並[將 MOF 檔案上傳到 Azure 自動化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然後從[內部部署](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[在 Azure 中](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms)註冊您的伺服器，以提取設定。

若要試用 PowerSTIG，請造訪 [PowerShell 資源庫](https://www.powershellgallery.com)並下載解決方案，或按一下 [專案網站] 以檢視[文件](https://github.com/microsoft/powerstig)。

## <a name="next-steps"></a>後續步驟

- 若要了解 PowerShell DSC，請參閱 [Windows PowerShell Desired State Configuration 概觀](/powershell/scripting/dsc/overview/overview)。
- 探索 [DSC 資源](/powershell/scripting/dsc/resources/resources)中的 PowerShell DSC 資源。
- 如需本機設定管理員設定的詳細資訊，請參閱[設定本機設定管理員](/powershell/scripting/dsc/managing-nodes/metaconfig)。
