---
title: Azure 自動化 Hybrid Runbook Worker
description: 本文提供有關安裝和使用混合式 Runbook 背景工作角色的資訊，它是 Azure 自動化的一項功能，可供您在本機資料中心內或雲端提供者的機器上用來執行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: cb1444261a2ba4810f4fddb3d7aa3bc172f09654
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278866"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>使用混合式 Runbook 背景工作角色將資料中心內或雲端的資源自動化

Azure 自動化中的 Runbook 可能無法存取其他雲端或內部部署環境中的資源，因為這些資源是在 Azure 雲端平台上執行。 您可以使用 Azure 自動化的混合式 Runbook 背景工作角色功能，直接在裝載角色的電腦上以及針對環境中的資源執行 Runbook，從而管理這些本機資源。 Runbook 會儲存並在 Azure 自動化中管理，接著傳遞至一或多個指派的電腦。

下圖說明這項功能：

![混合式 Runbook 背景工作概觀](media/automation-hybrid-runbook-worker/automation.png)

每一個混合式 Runbook 背景工作是您安裝代理程式時指定的混合式 Runbook 背景工作群組的成員。 群組可包含單一代理程式，但您可以在群組中安裝多個代理程式以獲得高可用性。 每部機器可以將一個混合式背景工作角色報告裝載到一個自動化帳戶。

在 Hybrid Runbook Worker 上啟動 Runbook 時，您會指定要執行它的群組。 每個群組中的背景工作角色會對 Azure 自動化進行輪詢，以查看是否有任何可用的作業。 若有可用的作業，會由第一個取得該作業的背景工作角色負責。 作業佇列的處理時間取決於混合式背景工作角色的硬體設定檔和負載。 您無法指定特定背景工作角色。 混合式 Runbook 背景工作角色不會共用 Azure 沙箱所具有的諸多限制。 它們在磁碟空間、記憶體或網路通訊端上並沒有相同的限制。 混合式 Runbook 背景工作角色只會受限於混合式 Runbook 背景工作角色本身上的資源。 此外，混合式 Runbook 背景工作角色不會共用 Azure 沙箱所具有的 180 分鐘[公平共用](automation-runbook-execution.md#fair-share)時間限制。 若要深入了解 Azure 沙箱和混合式 Runbook 背景工作角色的服務限制，請參閱作業[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)頁面。

## <a name="install-a-hybrid-runbook-worker"></a>安裝混合式 Runbook 背景工作角色

安裝混合式 Runbook 背景工作角色的程序取決於作業系統。 下表包含可用於安裝的方法連結。

若要安裝及設定 Windows 混合式 Runbook 背景工作角色，您可以使用兩種方法。 建議的方法是使用自動化 Runbook，將設定 Windows 電腦的程序完全自動化。 第二種方法採取逐步程序來手動安裝和設定角色。 若為 Linux 機器，您可以執行 Python 指令碼，在機器上安裝代理程式。

|OS  |部署類型  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[手動](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> 若要使用 Desired State Configuration (DSC) 來管理支援混合式 Runbook 背景工作角色的伺服器設定，您必須將它們新增為 DSC 節點。 如需有關讓它們上線以透過 DSC 進行管理的詳細資訊，請參閱[讓機器上線以透過 Azure 自動化 DSC 進行管理](automation-dsc-onboarding.md)。
>
>如果您啟用[更新管理解決方案](automation-update-management.md)，則任何連線到 Azure Log Analytics 工作區的電腦都會自動設定為混合式 Runbook 背景工作角色，以支援此解決方案所包含的 Runbook。 不過，該電腦不會向您已在自動化帳戶中定義的任何混合式背景工作角色群組註冊。 您可以將電腦新增至您自動化帳戶中的混合式 Runbook 背景工作角色群組來支援自動化 Runbook，只要解決方案和混合式 Runbook 背景工作角色群組成員資格兩者使用相同的帳戶即可。 此功能已新增至混合式 Runbook 背景工作角色 7.2.12024.0 版。

開始部署混合式 Runbook 背景工作角色之前，請先檢閱[規劃網路的相關資訊](#network-planning)。 在您成功部署背景工作角色之後，請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定 Runbook 將內部部署資料中心或其他雲端環境中的程序自動化。

您可以將電腦新增至您自動化帳戶中的混合式 Runbook 背景工作角色群組來支援自動化 Runbook，只要解決方案和混合式 Runbook 背景工作角色群組成員資格兩者使用相同的帳戶即可。 此功能已新增至 Hybrid Runbook Worker 7.2.12024.0 版。

## <a name="remove-a-hybrid-runbook-worker"></a>移除混合式 Runbook 背景工作角色

您可以移除群組中的一或多個混合式 Runbook 背景工作角色，或移除該群組，視您的需求而定。 若要從內部部署電腦中移除混合式 Runbook 背景工作角色，請使用下列步驟：

1. 在 Azure 入口網站中，前往您的自動化帳戶。
2. 在 [帳戶設定] 下，選取 [金鑰] 並記下 [URL] 和 [主要存取金鑰] 的值。 下一個步驟需要此資訊。

### <a name="windows"></a>Windows

在系統管理員模式中開啟 PowerShell 工作階段，並執行下列命令。 使用 **-Verbose** 參數可取得移除程序的詳細記錄。

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

若要從混合式背景工作角色群組中移除過時的機器，請使用選擇性的 `machineName` 參數。

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

您可以在混合式 Runbook 背景工作角色上使用 `ls /var/opt/microsoft/omsagent` 命令，以取得工作區識別碼。 目錄中有一個資料夾，其資料夾名稱就是工作區識別碼。

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> 此程式碼不會移除電腦上的 Microsoft Monitoring Agent，只會移除混合式 Runbook 背景工作角色的功能和設定。

## <a name="remove-a-hybrid-worker-group"></a>移除混合式背景工作角色群組

若要移除群組，您必須先使用先前所示的程序，從群組的每一部成員電腦中移除混合式 Runbook 背景工作角色。 然後，使用下列步驟移除群組：

1. 在 Azure 入口網站中，開啟自動化帳戶。
2. 在 [程序自動化] 底下選取 [混合式背景工作角色群組]。 選取您要刪除的群組。 該群組的屬性頁面隨即出現。

   ![屬性頁面](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. 在所選群組的屬性頁面上，選取 [刪除]。 這會顯示訊息要求您確認此動作。 如果您確定要繼續，請選取 [是]。

   ![確認訊息](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   此程序可能需要數秒鐘的時間完成。 您可以在功能表的 [通知] 底下追蹤其進度。

## <a name="network-planning"></a>設定您的網路

### <a name="hybrid-worker-role"></a>混合式背景工作角色

若要讓混合式 Runbook 背景工作角色連接到 Azure 自動化並向其註冊，它必須能夠存取本節所述的埠號碼和 Url。 此存取會在 Microsoft Monitoring Agent 連接到 Azure 監視器記錄檔[所需的埠和 url](../azure-monitor/platform/agent-windows.md)上。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

如果您使用 proxy 伺服器在代理程式和 Azure 自動化服務之間進行通訊，請確保可以存取適當的資源。 混合式 Runbook 背景工作角色和自動化服務要求的完成時間為30秒。 3次嘗試之後，要求將會失敗。 如果您使用防火牆來限制網際網路存取，您必須設定防火牆以允許存取。 如果您使用 Log Analytics 閘道作為 Proxy，請確保已針對混合式背景工作角色進行設定。 如需有關如何執行這項操作的指示，請參閱[為自動化混合式背景工作角色設定 Log Analytics 閘道](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway)。

若要讓混合式 Runbook 背景工作角色與自動化進行通訊，需要下列連接埠和 URL：

* 連接埠︰只需要 TCP 443 以便進行傳出網際網路存取。
* 全域 URL：*.azure-automation.net
* US Gov 維吉尼亞州的全域 URL： *.azure automation.us
* 代理程式服務： https://\<workspaceId\>.agentsvc.azure-automation.net

建議使用定義例外狀況時所列出的位址。 針對 IP 位址，您可以下載 [Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/en-us/download/details.aspx?id=56519)。 此檔案會每週更新，並具有目前已部署的範圍及任何即將進行的 IP 範圍變更。

如果您有針對特定區域定義的自動化帳戶，您可以將通訊限制為該區域資料中心。 下表提供每個區域的 DNS 記錄：

| **區域** | **DNS 記錄** |
| --- | --- |
| 美國中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 美國中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 美國東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 美國西部 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 西歐 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 北歐 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 東南亞 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 日本東部 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 澳大利亞中部 |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| 澳大利亞東部 |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| 澳大利亞東南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 英國南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov 維吉尼亞州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

如需區域 IP 位址 (而非名稱) 的清單，請從「Microsoft 下載中心」下載 [Azure 資料中心 IP 位址](https://www.microsoft.com/download/details.aspx?id=41653) XML 檔案。

> [!NOTE]
> Azure 資料中心 IP 位址 XML 檔會列出在 Microsoft Azure 資料中心使用的 IP 位址範圍。 此檔案包含計算、SQL 和儲存體範圍。
>
>每週會公佈已更新的檔案。 檔案會反映目前已部署的範圍及任何即將進行的 IP 範圍變更。 出現在檔案中的新範圍至少有一週的時間不會在資料中心中使用。
>
> 最好是每週下載新的 XML 檔案。 接著，更新您的網站以便正確地識別在 Azure 中執行的服務。 Azure ExpressRoute 使用者應該注意到，在每個月的第一週，此檔案會用來更新 Azure 空間的邊界閘道協定 (BGP) 公告。

### <a name="update-management"></a>更新管理

除了混合式 Runbook 背景工作角色所需的標準位址與連接埠之外，也需要下列位址 (尤其在更新管理時)。 這些位址的通訊皆經由連接埠 443 進行。

|Azure 公用  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>後續步驟

* 若要了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)。
* 若要了解如何針對混合式 Runbook 背景工作角色進行疑難排解，請參閱[針對混合式 Runbook 背景工作角色進行疑難排解](troubleshoot/hybrid-runbook-worker.md#general)

