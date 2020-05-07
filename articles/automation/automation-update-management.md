---
title: Azure 自動化中的更新管理
description: 本文說明管理 Windows 和 Linux 機器之更新的更新管理功能。
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: 24befd6e5d90c7c3fffd2049bae3bee392e6e0e0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855679"
---
# <a name="update-management-in-azure-automation"></a>Azure 自動化中的更新管理

您可以使用 Azure 自動化中的更新管理來管理 Azure、內部部署環境及其他雲端環境中 Windows 和 Linux 電腦的作業系統更新。 您可以快速評估所有代理程式電腦上可用更新的狀態，並管理為伺服器安裝必要更新的程式。

您可以使用下列方法來啟用虛擬機器（Vm）的更新管理：

* 從您的[Azure 自動化帳戶](automation-onboard-solutions-from-automation-account.md)，取得一或多個 Azure 機器。
* 針對非 Azure 機器手動進行。
* 針對來自 Azure 入口網站中 [虛擬機器] 頁面的單一 Azure VM。 此案例適用于[Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management)和[Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) vm。
* 針對[多個 Azure vm](manage-update-multi.md) ，請從 Azure 入口網站的 [虛擬機器] 頁面中選取它們。

> [!NOTE]
> 更新管理需要將 Log Analytics 工作區連結至您的自動化帳戶。 如需所支援區域的確切清單，請參閱[Azure 工作區](how-to/region-mappings.md)對應。 區域對應並不會影響從您的自動化帳戶在不同的區域中管理 Vm 的能力。

您可以使用[Azure Resource Manager 範本](automation-update-management-deploy-template.md)，協助您將更新管理部署到訂用帳戶中新的或現有的自動化帳戶和 Log Analytics 工作區。

> [!NOTE]
> 您無法使用以更新管理設定的電腦，從 Azure 自動化執行自訂腳本。 這部電腦只能執行 Microsoft 簽署的更新腳本。 

## <a name="update-management-overview"></a>更新管理概觀

受更新管理管理的電腦會使用下列設定來執行評估和更新部署：

* 適用于 Windows 或 Linux 的 Log Analytics 代理程式
* 適用於 Linux 的 PowerShell 預期狀態組態 (DSC)
* 自動化 Hybrid Runbook Worker
* 適用于 Windows 機器的 Microsoft Update 或 Windows Server Update Services （WSUS）

下圖說明更新管理如何評估和套用安全性更新至工作區中所有已連線的 Windows Server 和 Linux 電腦：

![更新管理工作流程](./media/automation-update-management/update-mgmt-updateworkflow.png)

您可以使用更新管理在相同租用戶中的多個訂用帳戶中以原生方式上架機器。

發行套件之後，需要2到3小時的時間，才會針對 Linux 電腦顯示修補程式以供評量。 對於 Windows 機器，需要12到15小時的時間，修補程式才會在發行後顯示進行評估。

在機器完成更新相容性掃描之後，代理程式會將大量資訊轉送至 Azure 監視器記錄。 在 Windows 電腦上，合規性掃描預設會每12小時執行一次。

除了掃描排程，更新相容性的掃描會在重新開機的 Log Analytics 代理程式的15分鐘內啟動、在更新安裝之前，以及在更新安裝之後啟動。

針對 Linux 電腦，預設會每小時執行合規性掃描。 如果重新開機 Log Analytics 代理程式，則會在15分鐘內啟動合規性掃描。

更新管理會報告機器如何根據您設定要同步處理的來源而定。 如果 Windows 電腦設定為向 WSUS 報告，視 WSUS 上次與 Microsoft Update 同步處理的時間而定，結果可能會與 Microsoft Update 顯示的不同。 針對設定為向本機存放庫（而不是公用存放庫）回報的 Linux 電腦，此行為是相同的。

> [!NOTE]
> 為了能正確地向服務回報，「更新管理」需要啟用特定 URL 和連接埠。 若要深入瞭解這些需求，請參閱[網路](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker#network-planning)設定。

您可以藉由建立排程的部署，在需要更新的電腦上部署和安裝軟體更新。 分類為選擇性的更新不會包含在 Windows 電腦的部署範圍中。 部署範圍中僅包含必要更新。

排定的部署會定義哪些目的電腦會收到適用的更新。 其方式是明確指定特定電腦，或選取以一組特定電腦的記錄搜尋為基礎的[電腦群組](https://docs.microsoft.com/azure/azure-monitor/platform/computer-groups)（或在根據指定準則動態選取 azure Vm 的[azure 查詢](automation-update-management-query-logs.md)上）。 這些群組與[範圍](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)設定不同，後者是用來控制接收設定以啟用更新管理的電腦目標。 這會使其無法執行和報告更新相容性，並安裝已核准的必要更新。

定義部署時，您也可以指定要核准的排程，並設定可安裝更新的時間週期。 這段期間稱為維護視窗。 維護期間的20分鐘範圍會保留重新開機，前提是需要一個，而且您已選取適當的重新開機選項。 如果修補所花費的時間超出預期，且維護期間內的時間少於20分鐘，就不會進行重新開機。

在 Azure 自動化中，會由 Runbook 安裝更新。 您無法查看這些 runbook，而且它們不需要任何設定。 建立更新部署時，它會建立一個排程，以在指定的時間為包含的機器啟動主要更新 runbook。 主要 Runbook 會在每個代理程式上啟動子 Runbook，以安裝必要的更新。

在更新部署中指定的日期和時間，目的電腦會以平行方式執行部署。 在安裝之前，系統會先執行掃描，以確認仍然需要更新。 對於 WSUS 用戶端機器，如果未在 WSUS 中核准更新，則更新部署會失敗。
不支援在一個以上的 Log Analytics 工作區（也稱為「多路連接」）中註冊更新管理的機器。

## <a name="clients"></a>用戶端

### <a name="supported-client-types"></a>支援的用戶端類型

下表列出更新評估支援的作業系統。 修補需要混合式 Runbook 背景工作角色。 如需混合式 Runbook 背景工作角色需求的詳細資訊，請參閱[部署 Windows 混合式 runbook 背景工作](automation-windows-hrw-install.md)角色和[部署 Linux 混合式 Runbook 背景工作角色](automation-linux-hrw-install.md)。

> [!NOTE]
> 只有在 [自動化帳戶] 和 [Log Analytics 工作區對應][資料表](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)中列出的特定區域，才支援 Linux 機器的更新評估。 

|作業系統  |備忘錄  |
|---------|---------|
|Windows Server 2019 （Datacenter/Datacenter Core/Standard）<br><br>Windows Server 2016 （Datacenter/Datacenter Core/Standard）<br><br>Windows Server 2012 R2 （Datacenter/Standard）<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 （RTM 和 SP1 標準）| 更新管理只支援此作業系統的評量。 不支援修補，因為 Windows Server 2008 R2 不支援[混合式 Runbook 背景工作角色](automation-windows-hrw-install.md)。 |
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理程式需要存取更新存放庫。 以分類為基礎的`yum`修補需要傳回 CentOS 在其 RTM 版本中沒有的安全性資料。 如需 CentOS 上以分類為基礎之修補的詳細資訊，請參閱[Linux 上的更新分類](automation-view-update-assessments.md#linux-2)。          |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理程式需要存取更新存放庫。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理程式需要存取更新存放庫。        |
|Ubuntu 14.04 LTS、16.04 LTS 和 18.04 (x86/x64)      |Linux 代理程式需要存取更新存放庫。         |

> [!NOTE]
> 您可以透過更新管理來管理 Azure 虛擬機器擴展集。 更新管理適用于實例本身，而不是基底映射。 您需要以累加方式排程更新，如此一來，就不會一次更新所有 VM 實例。
> 您可以遵循 [在[非 Azure 機器上架](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)] 底下的步驟來新增虛擬機器擴展集的節點。

### <a name="unsupported-client-types"></a>不支援的用戶端類型

下表列出不支援的作業系統：

|作業系統  |備忘錄  |
|---------|---------|
|Windows 用戶端     | 不支援用戶端作業系統 (例如 Windows 7 和 Windows 10)。        |
|Windows Server 2016 Nano Server     | 不支援。       |
|Azure Kubernetes Service 節點 | 不支援。 使用在[Azure Kubernetes Service 中將安全性和核心更新套用至 Linux 節點](../aks/node-updates-kured.md)中所述的修補程式（AKS）|

### <a name="client-requirements"></a>用戶端需求

下列資訊說明作業系統特定的用戶端需求。 如需其他指引，請參閱[網路規劃](#ports)。

#### <a name="windows"></a>Windows

Windows 代理程式必須設定為與 WSUS 伺服器通訊，或需要存取 Microsoft Update。 如需有關如何安裝適用于 Windows 的 Log Analytics 代理程式的詳細資訊，請參閱[將 Windows 電腦連線到 Azure 監視器](../log-analytics/log-analytics-windows-agent.md)。

您可以使用更新管理與 Microsoft 端點 Configuration Manager。 若要深入瞭解整合案例，請參閱[整合 Configuration Manager 與更新管理](updatemgmt-mecmintegration.md#configuration)。 適用于[windows 的 Log Analytics 代理程式](../azure-monitor/platform/agent-windows.md)是由您 Configuration Manager 環境中的網站所管理的 windows 伺服器所需。 

根據預設，從 Azure Marketplace 部署的 Windows Vm 會設定為從 Windows Update 服務接收自動更新。 當您將 Windows Vm 新增至工作區時，此行為不會變更。 如果您未使用更新管理主動管理更新，則會套用預設行為（自動套用更新）。

> [!NOTE]
> 您可以修改群組原則，讓電腦重新開機只能由使用者執行，而不是由系統執行。 如果更新管理沒有重新開機電腦的許可權，而沒有使用者的手動互動，則受管理的機器可能會停滯。 如需詳細資訊，請參閱[設定自動更新的群組原則設定](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)。

#### <a name="linux"></a>Linux

針對 Linux，電腦需要存取「私人」或「公用」的更新存放庫。 必須使用 TLS 1.1 或 TLS 1.2，才能與更新管理互動。 更新管理不支援適用于 Linux 且設定為向多個 Log Analytics 工作區報告的 Log Analytics 代理程式。 電腦也必須安裝 Python 2.x。

> [!NOTE]
> 只有特定區域支援 Linux 機器的更新評估。 請參閱自動化帳戶和 Log Analytics 工作區對應[表](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)。 

如需有關如何安裝適用于 Linux 的 Log Analytics 代理程式及下載最新版本的詳細資訊，請參閱[適用于 linux 的 Log analytics 代理程式](../azure-monitor/platform/agent-linux.md)。 

從 Azure Marketplace 中提供的隨選 Red Hat Enterprise Linux （RHEL）映射建立的 Vm，會進行註冊以存取部署在 Azure 中的[Red Hat 更新基礎結構（RHUI）](../virtual-machines/workloads/redhat/redhat-rhui.md) 。 任何其他 Linux 發行版本都必須使用散發套件支援的方法，從發佈的線上檔案存放庫更新。

## <a name="permissions"></a>權限

若要建立及管理更新部署，您必須具有特定權限。 若要瞭解這些許可權，請參閱以[角色為基礎的存取–更新管理](automation-role-based-access-control.md#update-management-permissions)。

## <a name="update-management-components"></a>更新管理元件

更新管理使用本節所述的資源。 當您啟用更新管理時，這些資源會自動新增至您的自動化帳戶。

### <a name="hybrid-runbook-worker-groups"></a>混合式 Runbook 背景工作群組

啟用更新管理之後，任何直接連線到 Log Analytics 工作區的 Windows 電腦都會自動設定為混合式 Runbook 背景工作角色，以支援支援更新管理的 runbook。

更新管理所管理的每部 Windows 電腦都會列在 [混合式背景工作角色群組] 窗格中，作為自動化帳戶的系統混合式背景工作角色群組。 這些群組會使用`Hostname FQDN_GUID`命名慣例。 您不能讓這些群組以您帳戶中的 Runbook 為目標。 如果您嘗試，則嘗試會失敗。 這些群組僅用於支援更新管理。

您可以將 Windows 電腦新增到自動化帳戶中的混合式 Runbook 背景工作角色群組，以便在更新管理和混合式 Runbook 背景工作角色群組成員資格使用相同的帳戶時支援自動化 runbook。 此功能已新增至混合式 Runbook 背景工作角色 7.2.12024.0 版。

### <a name="management-packs"></a>管理組件

如果您的 Operations Manager 管理群組已[連線到 Log Analytics 工作區](../azure-monitor/platform/om-agents.md)，則下列管理元件會安裝在 Operations Manager 中。 這些管理元件也會安裝在直接連線的 Windows 電腦上進行更新管理。 您不需要設定或管理這些管理組件。

* Microsoft System Center Advisor 更新評估智慧套件 (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新部署 MP

> [!NOTE]
> 如果您的 Operations Manager 1807 或2019管理群組連線到使用管理群組中設定之代理程式來收集記錄資料的 Log Analytics 工作區，則必須覆寫參數`IsAutoRegistrationEnabled` ，並在**microsoft.intelligencepacks.applicationdependencymonitor. AzureAutomation. microsoft.enterprisemanagement.healthservice.azureautomation.hybridagent 事件**中將它設定為 True。

如需有關管理元件更新的詳細資訊，請參閱[將 Operations Manager 連接到 Azure 監視器記錄](../azure-monitor/platform/om-agents.md)。

> [!NOTE]
> 若要讓更新管理使用 Log Analytics 代理程式完全管理電腦，您必須更新為適用于 Windows 的 Log Analytics 代理程式或適用于 Linux 的 Log Analytics 代理程式。 若要深入了解如何更新代理程式，請參閱[如何升級 Operations Manager 代理程式](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)。 在使用 Operations Manager 的環境中，您必須執行 System Center Operations Manager 2012 R2 UR 14 或更新版本。

## <a name="data-collection"></a>資料收集

### <a name="supported-sources"></a>支援的來源

下表描述更新管理支援的連線來源：

| 連線的來源 | 支援 | 描述 |
| --- | --- | --- |
| Windows 代理程式 |是 |更新管理會從 Windows 代理程式收集系統更新的相關資訊，然後開始安裝必要的更新。 |
| Linux 代理程式 |是 |更新管理會從 Linux 代理程式收集系統更新的相關資訊，然後在支援的發行版本上開始安裝必要的更新。 |
| Operations Manager 管理群組 |是 |「更新管理」會從所連線之管理群組中的代理程式收集系統更新的相關資訊。<br/><br/>不需要從 Operations Manager 代理程式直接連接到 Azure 監視器記錄。 資料會從管理群組轉送至 Log Analytics 工作區。 |

### <a name="collection-frequency"></a>收集頻率

更新管理會使用下列規則來掃描受管理電腦的資料。 儀表板可能需要30分鐘到6小時，才能顯示來自受管理電腦的更新資料。

* 每一部 Windows 電腦更新管理都會針對每部機器執行每天掃描兩次。 它會每隔15分鐘查詢 Windows API 的上次更新時間，以判斷狀態是否已變更。 如果狀態已變更，更新管理會啟動相容性掃描。

* 每個 Linux 機器更新管理都會每小時執行一次掃描。

使用更新管理 Azure 監視器電腦記錄的平均資料使用量大約是每月 25 MB。 這個值只是近似值，視您的環境而定，可能會變更。 我們建議您監視您的環境，以追蹤確切的使用方式。 如需分析資料使用量的詳細資訊，請參閱[管理使用量和成本](../azure-monitor/platform/manage-cost-storage.md)。

## <a name="network-planning"></a><a name="ports"></a>網路規劃

以下為「更新管理」特別需求的位址。 與這些位址的通訊皆經由連接埠 443 進行。

|Azure 公用  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

對於 Windows 機器，您也必須允許流量傳送至 Windows Update 所需的任何端點。 您可以在[與 HTTP/Proxy 相關的問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中，找到所需端點的更新清單。 如果您有本機[Windows Update 伺服器](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)，您也必須允許流向[WSUS 金鑰](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)中所指定伺服器的流量。

對於 Red Hat Linux 電腦，請參閱必要端點[的 RHUI 內容傳遞伺服器的 ip](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) 。 如需其他 Linux 發行版本，請參閱您的提供者檔。

如需混合式 Runbook 背景工作角色所需埠的詳細資訊，請參閱混合式背景[工作角色埠](automation-hybrid-runbook-worker.md#hybrid-worker-role)。

我們建議您在定義例外狀況時使用所列的位址。 針對 IP 位址，您可以下載[Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 此檔案會每週更新，並反映目前已部署的範圍及任何即將進行的 IP 範圍變更。

依照[連接沒有網際網路存取的電腦](../azure-monitor/platform/gateway.md)中的指示，設定無法存取網際網路的電腦。

## <a name="update-classifications"></a>更新分類

下表定義更新管理支援的 Windows 更新分類。 

|分類  |描述  |
|---------|---------|
|重大更新     | 特定問題的更新，負責處理與安全性無關的重大錯誤。        |
|安全性更新     | 特定產品的安全性相關更新。        |
|更新彙總套件     | 一組累計的 Hotfix，封裝在一起以便於部署。        |
|Feature Pack     | 在產品版本之外散發的新產品功能。        |
|Service Pack     | 一組套用到應用程式的累計 Hotfix。        |
|定義更新     | 病毒或其他定義檔案的更新。        |
|工具     | 有助於完成一或多個工作的公用程式或功能。        |
|更新     | 目前安裝之應用程式或檔案的更新。        |

下表定義支援的 Linux 更新分類。

|分類  |描述  |
|---------|---------|
|重大更新和安全性更新     | 特定問題或特定產品的安全性相關問題的更新，         |
|其他更新     | 本質上不重要或不是安全性更新的所有其他更新。        |

針對 Linux，更新管理可以區別雲端中的重大更新和安全性更新，同時顯示因雲端中的資料擴充而產生的評量資料。 針對修補，「更新管理」仰賴機器上可用的分類資料。 與其他散發套件不同的是，CentOS 不會在 RTM 版本中提供這項資訊。 如果您已將 CentOS 機器設定為傳回下列命令的安全性資料，更新管理可以根據分類進行修補。

```bash
sudo yum -q --security check-update
```

目前沒有支援的方法可在 CentOS 上啟用原生分類資料可用性。 此時，只有可能已自行啟用此功能的客戶才會提供最佳支援。 

若要將 Red Hat Enterprise 版本6上的更新分類，您必須安裝 yum-security 外掛程式。 在 Red Hat Enterprise Linux 7 上，外掛程式已是 yum 本身的一部分，因此不需要安裝任何專案。 如需詳細資訊，請參閱下列 Red Hat[知識文章](https://access.redhat.com/solutions/10021)。

## <a name="integrate-update-management-with-configuration-manager"></a>將更新管理與 Configuration Manager 整合

已投資 Microsoft Endpoint Configuration Manager 來管理電腦、伺服器和行動裝置的客戶也需依賴 Configuration Manager 的強度和成熟度，以協助管理軟體更新。 若要瞭解如何將更新管理與 Configuration Manager 整合，請參閱[整合 Configuration Manager 與更新管理](updatemgmt-mecmintegration.md)。

## <a name="third-party-updates-on-windows"></a>Windows 上的協力廠商更新

更新管理依賴本機設定的更新存放庫來更新受支援的 Windows 系統（WSUS 或 Windows Update）。 [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher)之類的工具可讓您使用 WSUS 匯入及發佈自訂更新。 此案例可讓更新管理以協力廠商軟體更新使用 Configuration Manager 作為其更新存放庫的電腦。 若要了解如何設定 Updates Publisher，請參閱[安裝Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher)。

## <a name="enable-update-management"></a>啟用更新管理

Azure [Resource Manager 範本](automation-update-management-deploy-template.md)可協助您將更新管理部署到新的或現有的自動化帳戶，並在您的訂用帳戶中 Azure 監視器 Log Analytics 工作區。 它不會設定應管理的電腦範圍，這會在使用範本之後以個別步驟執行。

以下是您可以啟用更新管理並選取要管理之電腦的方式：

* [從虛擬機器](automation-onboard-solutions-from-vm.md)。
* [從流覽多部電腦](automation-onboard-solutions-from-browse.md)。
* [從 Azure 自動化帳戶](automation-onboard-solutions.md)。

## <a name="next-steps"></a>後續步驟

請參閱 Azure 自動化[常見問題](automation-faq.md)，以查看有關更新管理的常見問題。
