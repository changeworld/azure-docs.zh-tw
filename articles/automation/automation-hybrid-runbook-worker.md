---
title: Azure 自動化混合式 Runbook 背景工作角色概觀
description: 此文章概述混合式 Runbook 背景工作角色，可供您用來在本機資料中心或雲端提供者的機器上執行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 11/23/2020
ms.topic: conceptual
ms.openlocfilehash: 7feac3ccb94cd8b4b0fab509477d4dbf772df2ae
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505523"
---
# <a name="hybrid-runbook-worker-overview"></a>混合式 Runbook 背景工作概觀

Azure 自動化中的 Runbook 可能無法存取其他雲端或內部部署環境中的資源，因為這些資源是在 Azure 雲端平台上執行。 您可以使用 Azure 自動化的混合式 Runbook 背景工作角色功能，直接在裝載角色的電腦上以及針對環境中的資源執行 runbook，以管理這些本機資源。 Runbook 會在 Azure 自動化中儲存和管理，然後傳遞至一或多個指派的電腦。

下圖說明這項功能：

![混合式 Runbook 背景工作概觀](media/automation-hybrid-runbook-worker/automation.png)

Runbook Worker 有兩種類型：系統和使用者。 下表說明兩者之間的差異。

|類型 | 說明 |
|-----|-------------|
|**系統** |支援一組隱藏的 runbook，這些 runbook 是設計用來在 Windows 和 Linux 電腦上安裝使用者指定更新的更新管理功能。<br> 這種類型的混合式 Runbook 背景工作角色不是混合式 Runbook 背景工作角色群組的成員，因此不會執行目標為 Runbook Worker 群組的 runbook。 |
|**使用者** |支援使用者定義的 runbook，可直接在一或多個 Runbook 背景工作角色群組成員的 Windows 和 Linux 電腦上執行。 |

混合式 Runbook 背景工作角色可在 Windows 或 Linux 作業系統上執行，而且此角色依賴 [Log analytics 代理程式](../azure-monitor/platform/log-analytics-agent.md) 向 Azure 監視器 [log analytics 工作區](../azure-monitor/platform/design-logs-deployment.md)報告。 工作區不僅能監視支援的作業系統的電腦，也會下載安裝混合式 Runbook 背景工作角色所需的元件。

當 Azure 自動化 [更新管理](./update-management/overview.md) 啟用時，任何連線到 Log Analytics 工作區的電腦都會自動設定為系統混合式 Runbook 背景工作角色。

每個使用者混合式 Runbook 背景工作角色都是您在安裝背景工作時所指定之混合式 Runbook 背景工作角色群組的成員。 群組可以包含單一背景工作角色，但您可以在群組中包含多個背景工作角色，以獲得高可用性。 每部電腦可以將一個混合式 Runbook 背景工作角色裝載到一個自動化帳戶;您無法跨多個自動化帳戶註冊混合式背景工作角色。 這是因為混合式背景工作角色只能接聽單一自動化帳戶中的作業。 針對裝載受更新管理管理之系統混合式 Runbook 背景工作角色的電腦，您可以將它們新增至混合式 Runbook 背景工作角色群組。 但是，您必須針對更新管理和混合式 Runbook 背景工作角色群組成員資格使用相同的自動化帳戶。

當您在使用者混合式 Runbook 背景工作角色上啟動 runbook 時，您要指定執行的群組。 每個群組中的背景工作角色會對 Azure 自動化進行輪詢，以查看是否有任何可用的作業。 若有可用的作業，會由第一個取得該作業的背景工作角色負責。 作業佇列的處理時間取決於混合式背景工作角色的硬體設定檔和負載。 您無法指定特定背景工作角色。 「混合式背景工作角色」適用于每隔30秒)  (輪詢機制，並遵循先出的第一層。 根據推送作業的時間，無論是哪一種混合式背景工作角色 ping，自動化服務都會挑選工作。 單一混合式背景工作角色通常可以針對每個 ping 收取四項作業 (也就是每隔30秒) 。 如果您的推播作業速率超過每30秒4秒，則混合式 Runbook 背景工作角色群組中的另一個混合式背景工作角色群組會收取作業的可能性很高。

若要控制混合式 Runbook 背景工作角色上的 runbook 分佈，以及觸發工作的時間或方式，您可以在自動化帳戶內針對不同的混合式 Runbook 背景工作角色群組註冊混合式背景工作角色群組。 針對特定群組或群組，將作業設為目標，以支援您的執行安排。

使用混合式 Runbook 背景工作角色，而不是 [Azure 沙箱](automation-runbook-execution.md#runbook-execution-environment)，因為其不會有許多沙箱在磁碟空間、記憶體或網路通訊端上的[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。 混合式背景工作角色的限制只與背景工作角色本身的資源相關。

> [!NOTE]
> 混合式 Runbook 背景工作角色不受限於 Azure 沙箱所具有的[公平共用](automation-runbook-execution.md#fair-share)時間限制。

## <a name="hybrid-runbook-worker-installation"></a>混合式 Runbook 背景工作角色安裝

安裝使用者混合式 Runbook 背景工作角色的程式取決於作業系統。 下表定義部署類型。

|作業系統  |部署類型  |
|---------|---------|
|Windows     | [自動化](automation-windows-hrw-install.md#automated-deployment)<br>[手動](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [手動](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Windows 電腦的建議安裝方法是使用 Azure 自動化的 runbook，將設定的程式完全自動化。 如果這不可行，您可以依照逐步程式，手動安裝和設定角色。 若為 Linux 機器，您可以執行 Python 指令碼，在機器上安裝代理程式。

## <a name="network-planning"></a><a name="network-planning"></a>網路規劃

若要讓系統和使用者混合式 Runbook 背景工作角色連線至 Azure 自動化並向其註冊，它必須能夠存取本節所述的埠號碼和 Url。 背景工作角色也必須能夠存取 [Log Analytics 代理程式所需的連接埠和 URL](../azure-monitor/platform/agent-windows.md)，以連線至 Azure 監視器 Log Analytics 工作區。

混合式 Runbook 背景工作角色需要下列連接埠和 URL：

* 連接埠︰只需要 TCP 443，即可存取輸出網際網路
* 全域 URL： `*.azure-automation.net`
* US Gov 維吉尼亞州的全域 URL： `*.azure-automation.us`
* 代理程式服務： `https://<workspaceId>.agentsvc.azure-automation.net`

如果您有針對特定區域定義的自動化帳戶，您可以限制混合式 Runbook 背景工作角色對該區域資料中心的通訊。 檢查 Azure 自動化針對必要的 DNS 記錄所 [使用的 dns 記錄](how-to/automation-region-dns-records.md) 。

### <a name="proxy-server-use"></a>使用 Proxy 伺服器

如果您使用 proxy 伺服器在 Azure 自動化和執行 Log Analytics 代理程式的電腦之間進行通訊，請確定可存取適當的資源。 混合式 Runbook 背景工作角色和自動化服務要求的逾時時間為 30 秒。 嘗試三次之後，要求就會失敗。

### <a name="firewall-use"></a>使用防火牆

如果您使用防火牆來限制對網際網路的存取，您必須將防火牆設定為允許存取。 如果您使用 Log Analytics 閘道作為 Proxy，請確保已針對混合式 Runbook 背景工作角色進行設定。 請參閱 [設定適用于自動化混合式 Runbook 背景工作角色的 Log Analytics 閘道](../azure-monitor/platform/gateway.md)。

### <a name="service-tags"></a>服務標籤

Azure 自動化支援 Azure 虛擬網路服務標籤，從服務標記 [GuestAndHybridManagement](../virtual-network/service-tags-overview.md)開始。 您可以使用服務標記來定義 [網路安全性群組](../virtual-network/network-security-groups-overview.md#security-rules) 或 [Azure 防火牆](../firewall/service-tags.md)上的網路存取控制。 當您建立安全性規則時，可以使用服務標記來取代特定的 IP 位址。 藉由在規則的適當來源或目的地欄位中指定服務標記名稱 **GuestAndHybridManagement**  ，您可以允許或拒絕自動化服務的流量。 此服務標籤不支援將 IP 範圍限制為特定區域，以允許更細微的控制。

Azure 自動化服務的服務標籤只會提供用於下列案例的 Ip：

* 從您的虛擬網路內觸發 webhook
* 允許您 VNet 上的混合式 Runbook 背景工作角色或狀態設定代理程式與自動化服務通訊

>[!NOTE]
>服務標記 **GuestAndHybridManagement** 目前不支援直接在混合式 Runbook 背景工作角色上執行 Azure 沙箱中的 runbook 作業。

## <a name="support-for-impact-level-5-il5"></a>支援 (IL5) 的影響等級5

Azure 自動化混合式 Runbook 背景工作角色可在 Azure Government 中使用，以支援下列兩個設定之一的影響層級5工作負載：

* [隔離的虛擬機器](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines)。 部署時，它們會取用該電腦的整個實體主機，提供支援 IL5 工作負載所需的必要隔離層級。

* [Azure 專用主機](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host)，可提供可裝載一或多個虛擬機器的實體伺服器，專用於一個 azure 訂用帳戶。

>[!NOTE]
>透過混合式 Runbook 背景工作角色的計算隔離適用于 Azure 商業和美國政府雲端。 

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色的更新管理位址

除了混合式 Runbook 背景工作角色所需的標準位址和埠之外，更新管理還有 [ [網路規劃](./update-management/overview.md#ports) ] 區段下所述的其他網路設定需求。

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色上的 Azure 自動化狀態設定

您可以在混合式 Runbook 背景工作角色上執行 [Azure 自動化狀態設定](automation-dsc-overview.md)。 若要管理支援混合式 Runbook 背景工作角色的伺服器設定，您必須將伺服器新增為 DSC 節點。 請參閱[讓機器能夠由 Azure 自動化狀態設定管理](automation-dsc-onboarding.md)。

## <a name="runbook-worker-limits"></a>Runbook Worker 限制

每個自動化帳戶的混合式背景工作角色群組數目上限為4000，適用于系統 & 使用者混合式背景工作角色。 如果您有超過4000部要管理的電腦，我們建議您建立額外的自動化帳戶。

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>在混合式 Runbook 背景工作角色上啟動 Runbook

您的 runbook 可能會管理本機電腦上的資源，或針對部署使用者混合式 Runbook 背景工作角色的本機環境中的資源執行。 在此情況下，您可以選擇在混合式背景工作角色上執行 Runbook，而不是在自動化帳戶中執行。 在混合式 Runbook 背景工作角色上執行的 Runbook，在結構上與您在自動化帳戶中執行的那些相同。 請參閱[在混合式 Runbook 背景工作角色上啟動 Runbook](automation-hrw-run-runbooks.md)。

### <a name="hybrid-runbook-worker-jobs"></a>混合式 Runbook 背景工作角色作業

混合式 Runbook 背景工作角色作業是在 Windows 上的本機 **System** 帳戶或在 Linux 上的 [nxautomation 帳戶](automation-runbook-execution.md#log-analytics-agent-for-linux)下執行。 Azure 自動化處理混合式 Runbook 背景工作角色上的作業，與在 Azure 沙箱中執行的作業不同。 請參閱 [Runbook 執行環境](automation-runbook-execution.md#runbook-execution-environment)。

如果混合式 Runbook 背景工作角色主機電腦重新開機，則任何執行中的 Runbook 作業都會從頭重新啟動，或是從 PowerShell 工作流程 Runbook 的最後一個檢查點重新啟動。 在 Runbook 作業重新啟動超過三次之後，其就會暫止。

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色的 Runbook 權限

因為它們會存取非 Azure 資源，所以在使用者混合式 Runbook 背景工作角色上執行的 runbook 無法使用一般由 runbook 對 Azure 資源進行驗證所使用的驗證機制。 Runbook 會提供自己的驗證給本機資源，或使用 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)來設定驗證。 您也可以指定執行身分帳戶以對所有 Runbook 提供使用者內容。

## <a name="view-system-hybrid-runbook-workers"></a>查看系統混合式 Runbook 背景工作角色

在 Windows 或 Linux 機器上啟用更新管理功能之後，您可以在 Azure 入口網站中清查系統混合式 Runbook 背景工作角色群組的清單。 您可以在入口網站中，從所選自動化帳戶的左側窗格中選取 [**混合** 式背景工作 **角色] 群組**，以查看最多2000的背景工作角色。

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="自動化帳戶的系統混合式背景工作角色群組頁面" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

如果您有超過2000的混合式背景工作角色，若要取得所有的混合式背景工作角色清單，您可以執行下列 PowerShell 腳本：

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>後續步驟

* 若要了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)。

* 若要了解如何對混合式 Runbook 背景工作角色進行疑難排解，請參閱[對混合式 Runbook 背景工作角色問題進行疑難排解](troubleshoot/hybrid-runbook-worker.md#general)。