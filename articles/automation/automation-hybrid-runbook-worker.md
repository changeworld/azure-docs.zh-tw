---
title: Azure 自動化混合式 Runbook 背景工作角色概觀
description: 此文章概述混合式 Runbook 背景工作角色，可供您用來在本機資料中心或雲端提供者的機器上執行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 07/16/2020
ms.topic: conceptual
ms.openlocfilehash: 4d29979e28140b728478d405db934cb41783f4b0
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448085"
---
# <a name="hybrid-runbook-worker-overview"></a>混合式 Runbook 背景工作概觀

Azure 自動化中的 Runbook 可能無法存取其他雲端或內部部署環境中的資源，因為這些資源是在 Azure 雲端平台上執行。 您可以使用 Azure 自動化的「混合式 Runbook 背景工作角色」功能，直接在裝載角色的電腦上執行 runbook，並針對環境中的資源來管理這些本機資源。 Runbook 會在 Azure 自動化中儲存及管理，然後傳遞至一或多個指派的機器。

下圖說明這項功能：

![混合式 Runbook 背景工作概觀](media/automation-hybrid-runbook-worker/automation.png)

混合式 Runbook 背景工作角色可以在 Windows 或 Linux 作業系統上執行。 這取決於向 Azure 監視器[Log analytics 工作區](../azure-monitor/platform/design-logs-deployment.md)報告的[log analytics 代理程式](../azure-monitor/platform/log-analytics-agent.md)。 工作區不僅是用來監視受支援作業系統的電腦，也可以下載混合式 Runbook 背景工作角色所需的元件。

每一個混合式 Runbook 背景工作是您安裝代理程式時指定的混合式 Runbook 背景工作群組的成員。 群組可包含單一代理程式，但您可以在群組中安裝多個代理程式以獲得高可用性。 每部電腦都可以裝載一個向一個自動化帳戶報告的混合式背景工作角色。

在 Hybrid Runbook Worker 上啟動 Runbook 時，您會指定要執行它的群組。 每個群組中的背景工作角色會對 Azure 自動化進行輪詢，以查看是否有任何可用的作業。 若有可用的作業，會由第一個取得該作業的背景工作角色負責。 作業佇列的處理時間取決於混合式背景工作角色的硬體設定檔和負載。 您無法指定特定背景工作角色。

使用混合式 Runbook 背景工作角色，而不是 [Azure 沙箱](automation-runbook-execution.md#runbook-execution-environment)，因為其不會有許多沙箱在磁碟空間、記憶體或網路通訊端上的[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。 混合式背景工作角色的限制只與背景工作角色本身的資源相關。

> [!NOTE]
> 混合式 Runbook 背景工作角色不受限於 Azure 沙箱所具有的[公平共用](automation-runbook-execution.md#fair-share)時間限制。

## <a name="hybrid-runbook-worker-installation"></a>混合式 Runbook 背景工作角色安裝

安裝混合式 Runbook 背景工作角色的程序取決於作業系統。 下表定義部署類型。

|作業系統  |部署類型  |
|---------|---------|
|Windows     | [自動化](automation-windows-hrw-install.md#automated-deployment)<br>[手動](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

建議的安裝方法是使用 Azure 自動化 runbook，將設定 Windows 電腦的程式完全自動化。 如果這不可行，您可以依照逐步程式來手動安裝和設定角色。 若為 Linux 機器，您可以執行 Python 指令碼，在機器上安裝代理程式。

## <a name="network-planning"></a><a name="network-planning"></a>網路規劃

若要讓混合式 Runbook 背景工作角色連線至 Azure 自動化並向其註冊，其必須能夠存取此節所述的連接埠號碼和 URL。 背景工作角色也必須能夠存取 [Log Analytics 代理程式所需的連接埠和 URL](../azure-monitor/platform/agent-windows.md)，以連線至 Azure 監視器 Log Analytics 工作區。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

混合式 Runbook 背景工作角色需要下列連接埠和 URL：

* 連接埠︰只需要 TCP 443，即可存取輸出網際網路
* 全域 URL：`*.azure-automation.net`
* US Gov 維吉尼亞州的全域 URL：`*.azure-automation.us`
* 代理程式服務：`https://<workspaceId>.agentsvc.azure-automation.net`

如果您有針對特定區域定義的自動化帳戶，您可以限制混合式 Runbook 背景工作角色對該區域資料中心的通訊。 針對必要的 DNS 記錄，檢查[Azure 自動化所使用的 dns 記錄](how-to/automation-region-dns-records.md)。

### <a name="proxy-server-use"></a>使用 Proxy 伺服器

如果您使用 proxy 伺服器在 Azure 自動化和執行 Log Analytics 代理程式的電腦之間進行通訊，請確定可以存取適當的資源。 混合式 Runbook 背景工作角色和自動化服務要求的逾時時間為 30 秒。 嘗試三次之後，要求就會失敗。

### <a name="firewall-use"></a>使用防火牆

如果您使用防火牆來限制網際網路存取，您必須設定防火牆以允許存取。 如果您使用 Log Analytics 閘道作為 Proxy，請確保已針對混合式 Runbook 背景工作角色進行設定。 請參閱[為自動化混合式背景工作角色設定 Log Analytics 閘道](../azure-monitor/platform/gateway.md) \(部分機器翻譯\)。

### <a name="service-tags"></a>服務標籤

Azure 自動化支援 Azure 虛擬網路服務標籤，從服務標記[GuestAndHybridManagement](../virtual-network/service-tags-overview.md)開始。 您可以使用服務標記來定義[網路安全性群組](../virtual-network/security-overview.md#security-rules)或[Azure 防火牆](../firewall/service-tags.md)上的網路存取控制。 當您建立安全性規則時，可以使用服務標記來取代特定的 IP 位址。 藉由在規則的適當 [來源] 或 [目的地] 欄位中指定服務標記名稱**GuestAndHybridManagement** ，您可以允許或拒絕自動化服務的流量。 此服務標記不支援將 IP 範圍限制在特定區域，以允許更細微的控制。

Azure 自動化服務的服務標記只會提供用於下列案例的 Ip：

* 從您的虛擬網路內觸發 webhook
* 允許您 VNet 上的混合式 Runbook 背景工作角色或狀態設定代理程式與自動化服務進行通訊

>[!NOTE]
>服務標籤**GuestAndHybridManagement**目前不支援在 Azure 沙箱中執行 runbook 作業，只能直接在混合式 Runbook 背景工作角色上執行。

## <a name="support-for-impact-level-5-il5"></a>影響等級5的支援（IL5）

Azure 自動化混合式 Runbook 背景工作角色可用於 Azure Government 中，以支援下列兩個設定之一的影響層級5工作負載：

* [隔離的虛擬機器](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines)。 部署時，會使用該 VM 的整個實體主機，以提供支援 IL5 工作負載所需的必要隔離層級。

* [Azure 專用主機](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-hosts)，可提供能夠裝載一或多個虛擬機器（專門用於一個 Azure 訂用帳戶）的實體伺服器。

>[!NOTE]
>透過混合式 Runbook 背景工作角色的計算隔離適用于 Azure 商業和美國政府雲端。 

## <a name="update-management-on-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色上的更新管理

啟用 Azure 自動化[更新管理](update-management/update-mgmt-overview.md)時，任何連線到 Log Analytics 工作區的電腦都會自動設定為混合式 Runbook 背景工作角色。 每個背景工作角色都可以支援以更新管理為目標的 Runbook。

以這種方式設定的機器不會向已在您的自動化帳戶中定義的任何混合式 Runbook 背景工作角色群組註冊。 您可以將電腦新增至混合式 Runbook 背景工作角色群組，但更新管理和混合式 Runbook 背景工作角色群組成員資格都必須使用相同的帳戶。 此功能已新增至混合式 Runbook 背景工作角色 7.2.12024.0 版。

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色的更新管理位址

在混合式 Runbook 背景工作角色所需的標準位址和埠上方，更新管理具有[網路規劃](update-management/update-mgmt-overview.md#ports)一節中所述的其他網路設定需求。

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色上的 Azure 自動化狀態設定

您可以在混合式 Runbook 背景工作角色上執行 [Azure 自動化狀態設定](automation-dsc-overview.md)。 若要管理支援混合式 Runbook 背景工作角色的伺服器設定，您必須將伺服器新增為 DSC 節點。 請參閱[讓機器能夠由 Azure 自動化狀態設定管理](automation-dsc-onboarding.md)。

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>在混合式 Runbook 背景工作角色上啟動 Runbook

您的 runbook 可能會管理本機電腦上的資源，或針對已部署混合式 Runbook 背景工作角色的本機環境中的資源執行。 在此情況下，您可以選擇在混合式背景工作角色上執行 Runbook，而不是在自動化帳戶中執行。 在混合式 Runbook 背景工作角色上執行的 Runbook，在結構上與您在自動化帳戶中執行的那些相同。 請參閱[在混合式 Runbook 背景工作角色上啟動 Runbook](automation-hrw-run-runbooks.md)。

### <a name="hybrid-runbook-worker-jobs"></a>混合式 Runbook 背景工作角色作業

混合式 Runbook 背景工作角色作業是在 Windows 上的本機 **System** 帳戶或在 Linux 上的 [nxautomation 帳戶](automation-runbook-execution.md#log-analytics-agent-for-linux)下執行。 Azure 自動化在混合式 Runbook 背景工作角色上處理作業，與在 Azure 沙箱中執行的作業有點不同。 請參閱 [Runbook 執行環境](automation-runbook-execution.md#runbook-execution-environment)。

如果混合式 Runbook 背景工作角色主機電腦重新開機，則任何執行中的 Runbook 作業都會從頭重新啟動，或是從 PowerShell 工作流程 Runbook 的最後一個檢查點重新啟動。 在 Runbook 作業重新啟動超過三次之後，其就會暫止。

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色的 Runbook 權限

由於其會存取非 Azure 資源，因此在混合式 Runbook 背景工作角色上執行的 Runbook 不能使用 Runbook 通常用來向 Azure 資源進行驗證的驗證機制。 Runbook 會提供自己的驗證給本機資源，或使用 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)來設定驗證。 您也可以指定執行身分帳戶以對所有 Runbook 提供使用者內容。

## <a name="next-steps"></a>後續步驟

* 若要了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)。

* 若要了解如何對混合式 Runbook 背景工作角色進行疑難排解，請參閱[對混合式 Runbook 背景工作角色問題進行疑難排解](troubleshoot/hybrid-runbook-worker.md#general)。
