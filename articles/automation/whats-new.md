---
title: Azure 自動化的新功能
description: 每月更新 Azure 自動化的重大更新。
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2021
ms.custom: references_regions
ms.openlocfilehash: 8b412fffdda1ce6a2023a8b9ae459a52986cd93d
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690858"
---
# <a name="whats-new-in-azure-automation"></a>Azure 自動化的新功能

Azure 自動化持續不斷改進。 為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

- 最新版本
- 已知問題
- 錯誤修正

此頁面會每月更新，因此請定期瀏覽。

## <a name="january-2021"></a>2021 年 1 月

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Azure 自動化的 runbook 從 TechNet 腳本中心移至 GitHub

**類型：** 方案變更

TechNet 腳本中心即將淘汰，且 Runbook 資源庫中裝載的所有 runbook 都已移至我們的 [自動化 GitHub 組織](https://github.com/azureautomation)。 如需詳細資訊，請參閱 [移至 GitHub Azure 自動化的 runbook](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)。

## <a name="december-2020"></a>2020 年 12 月

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure 自動化和更新管理 Private Link GA

**類型：** 新功能

Azure 自動化和更新管理支援宣佈為 Azure 全球和政府雲端的 GA。 Azure 自動化已啟用 Private Link 支援來安全地在混合式背景工作角色上執行 runbook，請使用更新管理來修補電腦、透過 webhook 叫用 runbook，以及使用狀態設定服務來維持機器的抱怨。 如需詳細資訊，請參閱 [Azure 自動化 Private Link 支援](https://azure.microsoft.com/updates/azure-automation-private-link)

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Azure 自動化分類為輔助功能上的等級-C 認證

**類型：** 新功能

Microsoft 產品的協助工具功能可協助機構解決全球協助工具需求。 在 [ [blog 公告](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) ] 頁面上，搜尋 **Azure 自動化** 以讀取自動化服務的協助工具一致性報告。

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>在阿拉伯聯合大公國北部中支援自動化和狀態設定 GA

**類型：** 新功能

自動化帳戶，並狀態設定阿拉伯聯合大公國北部區域中的可用性。 如需詳細資訊，請參閱 [公告](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/)。

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>在德國中西部中支援自動化和狀態設定 GA

**類型：** 新功能

德國西部區域的自動化帳戶和狀態設定可用性。 如需詳細資訊，請參閱 [公告](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/)。

### <a name="dsc-support-for-oracle-6-and-7"></a>適用于 Oracle 6 和7的 DSC 支援

**類型：** 新功能

使用自動化狀態設定管理 Oracle Linux 6 和7台電腦。 請參閱 [支援的 Linux 散發版本](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) 以取得檔的更新，以反映這些變更。

### <a name="public-preview-for-python3-runbooks-in-automation"></a>自動化中 Python3 runbook 的公開預覽

**類型：** 新功能

Azure 自動化現在可在 Azure 全球雲端的所有區域中，以公開預覽形式支援 Python 3 cloud & 混合式 runbook 執行。 如需詳細資訊，請參閱 [公告] ( # B1 https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) 。

## <a name="november-2020"></a>2020 年 11 月

### <a name="dsc-support-for-ubuntu-1804"></a>適用于 Ubuntu 18.04 的 DSC 支援

**類型：** 新功能

請參閱 [支援的 Linux 散發版本](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) ，以取得反映這些變更之檔的更新。

## <a name="october-2020"></a>2020 年 10 月

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>在瑞士北部中支援自動化和狀態設定 GA

**類型：** 新功能

自動化帳戶，並狀態設定瑞士北部中的可用性。 如需詳細資訊，請參閱 [公告](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/)。

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>巴西中南部的自動化和狀態設定 GA 支援

**類型：** 新功能

巴西中南部的自動化帳戶和狀態設定可用性。 如需詳細資訊，請參閱 [公告](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/)。

### <a name="update-management-availability-in-south-central-us"></a>美國中南部更新管理的可用性

**類型：** 新功能

Azure 自動化區域對應已更新為支援美國中南部區域的更新管理功能。 請參閱 [支援的區域對應](how-to/region-mappings.md#supported-mappings) 以取得檔的更新，以反映此變更。

## <a name="september-2020"></a>2020 年 9 月

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>停機期間啟動/停止 VM runbook 更新為使用 Azure Az 模組

**類型：** 新功能

啟動/停止 VM runbook 已更新為使用 Az 模組來取代 Azure Resource Manager 模組。 如需檔的更新，請參閱 [停機期間啟動/停止 VM](automation-solution-vm-management.md) 總覽，以反映這些變更。

## <a name="august-2020"></a>2020 年 8 月

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>發佈 DSC 延伸模組以支援 Azure Arc

**類型：** 新功能

使用 Azure 自動化狀態設定來集中儲存設定，並透過已啟用 Azure Arc 的伺服器 DSC VM 延伸模組來維護混合式連線機器所需的狀態。 如需詳細資訊，請參閱 [啟用 Arc 的伺服器 VM 延伸模組總覽](../azure-arc/servers/manage-vm-extensions.md)。

### <a name="july-2020"></a>2020 年 7 月

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>引進自動化中 Private Link 支援的公開預覽

**類型：** 新功能

使用 Azure Private Link 將虛擬網路安全地連接到使用私人端點的 Azure 自動化。 如需詳細資訊，請閱讀 [公告](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/)。

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>適用于 Windows Server 2008 R2 的混合式 Runbook 背景工作角色支援

**類型：** 新功能

自動化混合式 Runbook 背景工作角色支援 Windows Server 2008 R2 作業系統。 請參閱 [支援的作業系統](automation-windows-hrw-install.md#supported-windows-operating-system) ，以取得檔的更新，以反映這些變更。

### <a name="update-management-support-for-windows-server-2008-r2"></a>更新管理 Windows Server 2008 R2 的支援

**類型：** 新功能

更新管理支援評定和修補 Windows Server 2008 R2 作業系統。 請參閱 [支援的作業系統](update-management/overview.md#clients) ，以取得檔的更新，以反映這些變更。

### <a name="automation-diagnostic-logs-schema-update"></a>自動化診斷記錄架構更新

**類型：** 新功能

變更 Log Analytics 服務中 Azure 自動化記錄資料的架構。 若要深入瞭解，請參閱 [將 Azure 自動化作業資料轉送至 Azure 監視器記錄](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object)。

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse 支援 Automation 更新管理

**類型：** 新功能

Azure Lighthouse 使用服務提供者和客戶的更新管理來啟用委派的資源管理。 您可以在[這裡](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/)深入了解.

## <a name="june-2020"></a>2020 年 6 月

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>US Gov 亞利桑那州區域中的自動化和更新管理可用性

**類型：** 新功能

自動化帳戶和更新管理可在 US Gov 亞利桑那州中取得。 如需詳細資訊，請參閱 [公告](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/)。

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>混合式 Runbook 背景工作角色上架腳本已更新為使用 Az 模組

**類型：** 新功能

New-OnPremiseHybridWorker 的 runbook 已更新為支援 Az 模組。 如需詳細資訊，請參閱 [PowerShell 資源庫](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7)中的套件。

### <a name="update-management-availability-in-china-east-2"></a>更新管理中國東部2中的可用性

**類型：** 新功能

已更新 Azure 自動化區域對應，以支援中國東部2區域中的更新管理功能。 請參閱 [支援的區域對應](how-to/region-mappings.md#supported-mappings) 以取得檔的更新，以反映此變更。

## <a name="may-2020"></a>2020 年 5 月

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>將自動化服務 DNS 記錄從特定區域更新為自動化帳戶特定 Url

**類型：** 新功能

Azure 自動化 DNS 記錄已更新為支援私用連結。 如需詳細資訊，請閱讀 [公告](https://azure.microsoft.com/updates/azure-automation-updateddns-records/)。

### <a name="added-capability-to-keep-automation-runbooks--dsc-scripts-encrypted-by-default"></a>已新增功能，讓自動化 runbook & 預設加密的 DSC 腳本

**類型：** 新功能

除了改善資產的安全性之外，也會加密 DSC 腳本 & 的 runbook，以增強 Azure 自動化安全性。

## <a name="april-2020"></a>2020 年 4 月

### <a name="retirement-of-the-automation-watcher-task"></a>停用自動化監看員工作

**類型：** 方案變更

Azure Logic Apps 現在是監視事件、排程週期性工作和觸發動作的建議和支援方法。 在監看員工作功能中將不會有進一步的投資。 若要深入瞭解，請參閱 [使用 Logic Apps 排程和執行循環性自動化](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)工作。

## <a name="march-2020"></a>2020 年 3 月

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>在 Azure 商業和政府雲端中 (IL5) 計算隔離的影響等級5支援

**型別：**

Azure 自動化混合式 Runbook 背景工作角色可在 Azure Government 中用來支援影響等級5的工作負載。 若要深入瞭解，請參閱我們的 [檔](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5)。

## <a name="february-2020"></a>2020 年 2 月

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>推出對 Azure 虛擬網路服務標記的支援

**類型：** 新功能

針對一部分案例，服務標籤的自動化支援允許或拒絕自動化服務的流量。 若要深入了解，請參閱[文件](automation-hybrid-runbook-worker.md#service-tags)。

### <a name="enable-tls-12-support-for-azure-automation-service"></a>啟用 Azure 自動化 service 的 TLS 1.2 支援

**類型：** 方案變更

Azure 自動化完全支援透過 webhook、DSC 節點和混合式背景工作角色)  (的 TLS 1.2 和所有用戶端呼叫。 仍支援 TLS 1.1 和 TLS 1.0 以提供與舊版用戶端的回溯相容性，直到客戶標準化並完整遷移至 TLS 1.2 為止。

## <a name="january-2020"></a>2020 年 1 月

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>針對 Azure 自動化引進客戶管理金鑰的公開預覽

**類型：** 新功能

客戶可以使用自己的受控金鑰來管理 Azure 自動化資產的加密，並加以保護。 如需詳細資訊，請參閱 [使用客戶管理的金鑰](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)。

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>淘汰 Azure 服務管理 (適用于 Azure 自動化的 ASM) REST Api

**輸入：** 退休

適用于 Azure 自動化的 Azure 服務管理 (ASM) REST Api 將于2020年1月30日淘汰，不再支援。 若要深入瞭解，請參閱 [公告](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/)。

## <a name="next-steps"></a>下一步

如果您想要參與 Azure 自動化檔，請參閱檔 [參與者指南](/contribute/)。
