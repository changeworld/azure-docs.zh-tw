---
title: Azure 自動化中的混合式 Runbook 背景工作總覽
description: 本文概述混合式 Runbook 背景工作角色，這是一項 Azure 自動化的功能，可讓您用來在本機資料中心或雲端提供者的電腦上執行 runbook。
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 0b36651a40267ec3ea8bfe7285c5f6c5d5c31562
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871651"
---
# <a name="hybrid-runbook-worker-overview"></a>混合式 Runbook 背景工作概觀

Azure 自動化中的 Runbook 可能無法存取其他雲端或內部部署環境中的資源，因為這些資源是在 Azure 雲端平台上執行。 您可以使用 Azure 自動化的混合式 Runbook 背景工作角色功能，直接在裝載角色的電腦上以及針對環境中的資源執行 Runbook，從而管理這些本機資源。 Runbook 會儲存並在 Azure 自動化中管理，接著傳遞至一或多個指派的電腦。

下圖說明這項功能：

![混合式 Runbook 背景工作概觀](media/automation-hybrid-runbook-worker/automation.png)

混合式 Runbook 背景工作角色可以執行 Windows 或 Linux 作業系統。 若要進行監視，它需要針對支援的作業系統使用 Azure 監視器和 Log Analytics 代理程式。 如需詳細資訊，請參閱 [Azure 監視器](automation-runbook-execution.md#azure-monitor)。

每一個混合式 Runbook 背景工作是您安裝代理程式時指定的混合式 Runbook 背景工作群組的成員。 群組可包含單一代理程式，但您可以在群組中安裝多個代理程式以獲得高可用性。 每部機器可以將一個混合式背景工作角色報告裝載到一個自動化帳戶。 

在 Hybrid Runbook Worker 上啟動 Runbook 時，您會指定要執行它的群組。 每個群組中的背景工作角色會對 Azure 自動化進行輪詢，以查看是否有任何可用的作業。 若有可用的作業，會由第一個取得該作業的背景工作角色負責。 作業佇列的處理時間取決於混合式背景工作角色的硬體設定檔和負載。 您無法指定特定背景工作角色。 

使用混合式 Runbook 背景工作角色，而不是[Azure 沙箱](automation-runbook-execution.md#runbook-execution-environment)，因為它沒有許多對磁碟空間、記憶體或網路通訊端的沙箱[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。 混合式背景工作角色的限制只與工作者本身的資源相關。 

> [!NOTE]
> 混合式 Runbook 背景工作角色未受限於 Azure 沙箱所擁有的[公平共用](automation-runbook-execution.md#fair-share)時間限制。 

## <a name="hybrid-runbook-worker-installation"></a>混合式 Runbook 背景工作安裝

安裝混合式 Runbook 背景工作角色的程式取決於作業系統。 下表定義部署類型。

|作業系統  |部署類型  |
|---------|---------|
|Windows     | [自動化](automation-windows-hrw-install.md#automated-deployment)<br>[手動](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

建議的安裝方法是使用 Azure 自動化 runbook，將設定 Windows 電腦的程式完全自動化。 第二種方法是遵循逐步程式來手動安裝和設定角色。 若為 Linux 機器，您可以執行 Python 指令碼，在機器上安裝代理程式。

## <a name="network-configuration"></a><a name="network-planning"></a>網路組態

若要讓混合式 Runbook 背景工作角色連接到 Azure 自動化並向其註冊，它必須能夠存取本節所述的埠號碼和 Url。 背景工作角色也必須能夠存取 Log Analytics 代理程式連線到 Azure 監視器 Log Analytics 工作區[所需的埠和 url](../azure-monitor/platform/agent-windows.md) 。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

混合式 Runbook 背景工作角色需要下列埠和 Url：

* 埠：只需要 TCP 443 才能存取輸出網際網路
* 全域 URL：*.azure-automation.net
* US Gov 維吉尼亞州的全域 URL： *.azure automation.us
* 代理程式服務：https://\<workspaceId\>.agentsvc.azure-automation.net

我們建議您在定義[例外](automation-runbook-execution.md#exceptions)狀況時使用所列的位址。 針對 IP 位址，您可以下載[Microsoft Azure DATACENTER IP 範圍](https://www.microsoft.com/en-us/download/details.aspx?id=56519)。 此檔案會每週更新，並具有目前已部署的範圍及任何即將進行的 IP 範圍變更。

### <a name="dns-records-per-region"></a>每個區域的 DNS 記錄

如果您有針對特定區域定義的自動化帳戶，您可以限制與該區域資料中心的混合式 Runbook 背景工作角色通訊。 下表提供每個區域的 DNS 記錄。

| **區域** | **DNS 記錄** |
| --- | --- |
| 澳大利亞中部 |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| 澳大利亞東部 |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| 澳大利亞東南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 美國東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 日本東部 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 北歐 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 美國中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 東南亞 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 英國南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov 維吉尼亞州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| 美國中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 西歐 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 美國西部 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

如需區域 IP 位址 (而非名稱) 的清單，請從「Microsoft 下載中心」下載 [Azure 資料中心 IP 位址](https://www.microsoft.com/download/details.aspx?id=41653) XML 檔案。 每週張貼更新的 IP 位址檔案。 

IP 位址檔案會列出 Microsoft Azure 資料中心所使用的 IP 位址範圍。 其中包括計算、SQL 和儲存體範圍，並反映目前已部署的範圍，以及任何即將進行的 IP 範圍變更。 出現在檔案中的新範圍至少有一週的時間不會在資料中心中使用。

每週下載新的 IP 位址檔案是個不錯的主意。 接著，更新您的網站以便正確地識別在 Azure 中執行的服務。 

> [!NOTE]
> 如果您使用 Azure ExpressRoute，請記住，在每個月的第一周中，IP 位址檔案用來更新 Azure 空間的邊界閘道協定（BGP）公告。

### <a name="proxy-server-use"></a>Proxy 伺服器使用

如果您使用 proxy 伺服器進行 Azure 自動化與 Log Analytics 代理程式之間的通訊，請確保可以存取適當的資源。 混合式 Runbook 背景工作角色和自動化服務要求的超時時間為30秒。 嘗試三次之後，要求就會失敗。 

### <a name="firewall-use"></a>防火牆使用

如果您使用防火牆來限制對網際網路的存取，您必須將防火牆設定為允許存取。 如果使用 Log Analytics 閘道作為 proxy，請確定它已針對混合式 Runbook 背景工作角色進行設定。 請參閱[為自動化混合式背景工作角色設定 Log Analytics 閘道](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway)。

## <a name="update-management-on-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色上的更新管理

啟用 Azure 自動化[更新管理](automation-update-management.md)時，任何連線到 Log Analytics 工作區的電腦都會自動設定為混合式 Runbook 背景工作角色。 每個背景工作角色都可以支援以更新管理為目標的 runbook。 

以這種方式設定的電腦不會向已在您的自動化帳戶中定義的任何混合式 Runbook 背景工作角色群組註冊。 您可以將電腦新增至混合式 Runbook 背景工作角色群組，但更新管理和混合式 Runbook 背景工作角色群組成員資格都必須使用相同的帳戶。 這項功能已新增至混合式 Runbook 背景工作角色的版本7.2.12024.0 版。

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色的更新管理位址

在混合式 Runbook 背景工作角色所需的標準位址和埠上方，更新管理需要下表中的位址。 這些位址的通訊會使用埠443。

|Azure 公用  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|

## <a name="state-configuration-dsc-on-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色上的狀態設定（DSC）

您可以在混合式 Runbook 背景工作角色上執行[狀態設定（DSC）](automation-dsc-overview.md)功能。 若要管理支援混合式 Runbook 背景工作角色的伺服器設定，您必須將伺服器新增為 DSC 節點。 如需有關上架的詳細資訊，請參閱將[機器上架以供狀態設定（DSC）管理](automation-dsc-onboarding.md)。

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色上的 runbook

您的 runbook 可能會管理本機電腦上的資源，或針對已部署混合式 Runbook 背景工作角色的本機環境中的資源執行。 在此情況下，您可以選擇在混合式背景工作角色上執行 runbook，而不是在自動化帳戶中執行。 在混合式 Runbook 背景工作角色上執行的 runbook，在結構上與您在自動化帳戶中執行的相同。 請參閱[在混合式 Runbook 背景工作角色上執行 runbook](automation-hrw-run-runbooks.md)。

### <a name="hybrid-runbook-worker-jobs"></a>混合式 Runbook 背景工作角色工作

混合式 Runbook 背景工作角色作業會在 Windows 上的本機**系統**帳戶或 Linux 上的[nxautomation 帳戶](automation-runbook-execution.md#log-analytics-agent-for-linux)下執行。 Azure 自動化在混合式 Runbook 背景工作角色上處理工作，與在 Azure 沙箱中執行的作業有點不同。 請參閱[Runbook 執行環境](automation-runbook-execution.md#runbook-execution-environment)。

如果混合式 Runbook 背景工作角色主機電腦重新開機，任何執行中的 runbook 作業會從一開始或從 PowerShell 工作流程 runbook 的最後一個檢查點重新開機。 在 runbook 作業重新開機超過三次之後，它就會暫停。

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色的 runbook 許可權

由於它們會存取非 Azure 資源，因此在混合式 Runbook 背景工作角色上執行的 runbook 不能使用驗證機制，這通常是用來向 Azure 資源進行驗證的 runbook。 Runbook 會對本機資源提供自己的驗證，或使用[Azure 資源的受控](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)識別來設定驗證。 您也可以指定執行身分帳戶，為所有 runbook 提供使用者內容。

## <a name="next-steps"></a>後續步驟

* 若要了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)。
* 若要瞭解如何針對混合式 Runbook 背景工作角色進行疑難排解，請參閱[疑難排解混合式 runbook 背景工作角色](troubleshoot/hybrid-runbook-worker.md#general)。