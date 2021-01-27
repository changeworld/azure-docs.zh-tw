---
title: Azure 自動化網路設定詳細資料
description: 本文提供 Azure 自動化狀態設定、Azure 自動化混合式 Runbook 背景工作角色、更新管理，以及變更追蹤和清查所需的網路資訊詳細資料
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 0add7eed6abbe6c137d423ee4a7ef5f0f60072e3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900145"
---
# <a name="azure-automation-network-configuration-details"></a>Azure 自動化網路設定詳細資料

此頁面會提供 [混合式 Runbook 背景工作角色和狀態設定](#hybrid-runbook-worker-and-state-configuration)所需的網路詳細資料，以及 [更新管理和變更追蹤和清查](#update-management-and-change-tracking-and-inventory)所需的網路詳細資料。

## <a name="hybrid-runbook-worker-and-state-configuration"></a>混合式 Runbook 背景工作角色和狀態設定

混合式 Runbook 背景工作角色需要下列埠和 Url，而且 [Automation 狀態設定](automation-dsc-overview.md) 與 Azure 自動化通訊。

* 埠：輸出網際網路存取只需要443
* 全域 URL： `*.azure-automation.net`
* US Gov 維吉尼亞州的全域 URL： `*.azure-automation.us`
* 代理程式服務： `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色的網路規劃

針對要連接到 Azure 自動化的系統或使用者混合式 Runbook 背景工作角色，必須能夠存取本節所述的埠號碼和 Url。 背景工作角色也必須能夠存取 Log Analytics 代理程式連接到 Azure 監視器 Log Analytics 工作區 [所需的埠和 url](../azure-monitor/platform/agent-windows.md) 。

如果您有針對特定區域定義的自動化帳戶，您可以限制混合式 Runbook 背景工作角色對該區域資料中心的通訊。 檢查 Azure 自動化針對必要的 DNS 記錄所 [使用的 dns 記錄](how-to/automation-region-dns-records.md) 。

### <a name="configuration-of-private-networks-for-state-configuration"></a>狀態設定的私人網路設定

如果您的節點位於私人網路中，則需要上面定義的埠和 Url。 這些資源會為受控節點提供網路連線能力，並允許 DSC 與 Azure 自動化進行通訊。

如果您使用在節點之間進行通訊的 DSC 資源 (例如 [WaitFor* 資源](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource))，則也必須允許節點之間的流量。 請參閱每個 DSC 資源的文件，以了解這些網路需求。

若要瞭解 TLS 1.2 的用戶端需求，請參閱 [Azure 自動化的 tls 1.2 強制](automation-managing-data.md#tls-12-enforcement-for-azure-automation)。

## <a name="update-management-and-change-tracking-and-inventory"></a>更新管理和變更追蹤和清查

更新管理和變更追蹤和清查都需要此資料表中的位址。 資料表後面的段落也適用于兩者。

這些位址的通訊會使用 **埠 443**。

|Azure 公用  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*.ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*. azure-automation.us|

當您建立網路群組安全性規則或設定 Azure 防火牆以允許流量流向自動化服務和 Log Analytics 工作區時，請使用 [服務](../virtual-network/service-tags-overview.md#available-service-tags)標籤 **GuestAndHybridManagement** 和 **AzureMonitor**。 這可簡化網路安全性規則的持續管理。 若要安全且私下地從您的 Azure Vm 連接到自動化服務，請參閱 [使用 Azure Private Link](./how-to/private-link-security.md)。 若要取得目前的服務標籤和範圍資訊，以納入您的內部部署防火牆設定，請參閱 [可下載的 JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)檔案。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [自動化更新管理總覽](update-management\overview.md)。
* 深入瞭解 [混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)。
* 瞭解 [變更追蹤和清查](change-tracking\overview.md)。
* 瞭解 [自動化狀態設定](automation-dsc-overview.md)。
