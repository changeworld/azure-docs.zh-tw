---
title: Azure 自動化更新管理概觀
description: 此文章提供可對 Windows 和 Linux 機器實作更新的更新管理功能概觀。
services: automation
ms.subservice: update-management
ms.date: 12/09/2020
ms.topic: conceptual
ms.openlocfilehash: 4b557c9772e76b6b61cdf01799ee30ba6bc11807
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928421"
---
# <a name="update-management-overview"></a>更新管理概觀

您可以使用 Azure 自動化中的更新管理來管理 Azure、內部部署環境和其他雲端環境中 Windows 和 Linux 虛擬機器的作業系統更新。 您可以快速評估所有代理程式機器上可用更新的狀態，並管理為伺服器安裝必要更新的程序。

> [!NOTE]
> 您無法使用以更新管理設定的機器，從 Azure 自動化中執行自訂指令碼。 這部機器只能執行 Microsoft 簽署的更新指令碼。

> [!NOTE]
> 目前不支援直接從已啟用 Arc 的伺服器啟用更新管理。 請參閱[從您的自動化帳戶啟用更新管理](../../automation/update-management/enable-from-automation-account.md)以了解需求，以及如何為您的伺服器啟用此功能。

若要在 Azure VM 上自動下載並安裝可用的 *重大* 和 *安全性* 修補程式，請參閱 WINDOWS vm 的 [自動 VM 來賓修補](../../virtual-machines/windows/automatic-vm-guest-patching.md) 。

在部署更新管理並啟用您的機器以進行管理之前，請確定您瞭解下列各節中的資訊。  

## <a name="about-update-management"></a>關於更新管理

受更新管理管理的機器會依賴下列各項來執行評估和部署更新：

* 適用于 Windows 或 Linux 的[Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)
* 適用於 Linux 的 PowerShell 預期狀態組態 (DSC)
* 當您在電腦上啟用更新管理時，自動化混合式 Runbook 背景工作 (會自動安裝) 
* Microsoft Update 或 [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (適用于 Windows 電腦的 WSUS) 
* 適用于 Linux 機器的私人或公用更新儲存機制

下圖說明更新管理如何評估並將安全性更新套用至工作區中所有連線的 Windows Server 和 Linux 伺服器：

![更新管理工作流程](./media/overview/update-mgmt-updateworkflow.png)

更新管理可以用來以原生方式部署至相同租使用者中多個訂用帳戶中的電腦。

針對 Linux 機器，發行套件之後，需要 2 到 3 小時的時間，才會顯示修補程式以進行評量。 針對 Windows 機器，在發行之後，需要 12 到 15 小時的時間，才會顯示修補程式以進行評量。 當電腦完成更新合規性掃描時，代理程式會將大量資訊轉送到 Azure 監視器記錄。 在 Windows 機器上，合規性掃描預設每 12 小時執行一次。 針對 Linux 機器，合規性掃描預設每小時執行一次。 如果重新啟動 Log Analytics 代理程式，則會在 15 分鐘內啟動合規性掃描。

除了掃描排程，如果在更新安裝之前與更新安裝之後重新啟動 Log Analytics 代理程式，則會在 15 分鐘內啟動更新合規性掃描。

更新管理會根據您設定要同步處理的來源，回報機器的最新狀態。 如果 Windows 電腦設定為向 [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS) 報告，視 wsus 上次與 Microsoft Update 同步處理的時間而定，結果可能會與 Microsoft Update 所顯示的不同。 針對設定為向本機存放庫 (而非公用存放庫) 回報的 Linux 機器，此行為也相同。

> [!NOTE]
> 為了能正確地向服務回報，「更新管理」需要啟用特定 URL 和連接埠。 若要深入了解這些需求，請參閱[網路設定](../automation-hybrid-runbook-worker.md#network-planning) \(部分機器翻譯\)。

您可以藉由建立排定的部署，在需要更新的機器上部署和安裝軟體更新。 歸類為選擇性的更新不會包含在 Windows 機器的部署範圍內。 部署範圍中僅包含必要更新。

排定的部署會定義哪些目標機器要收到適用的更新。 這會藉由明確指定特定的機器，或根據一組特定機器的記錄搜尋選取[電腦群組](../../azure-monitor/platform/computer-groups.md) \(部分機器翻譯\) (或根據依指定準則動態選取 Azure VM 的 [Azure 查詢](query-logs.md)) 來執行。 這些群組與[範圍設定](../../azure-monitor/insights/solution-targeting.md) \(部分機器翻譯\) 不同，其可用來控制接收設定以啟用更新管理之機器的目標。 這可防止其執行及回報更新合規性，以及安裝已核准的必要更新。

定義部署時，您也可以指定排程，以核准並設定一段可安裝更新的期間。 這段期間稱為維護時間範圍。 假設您需要維護且已選取適當的重新開機選項，則會保留 20 分鐘的維護時間範圍來重新開機。 如果修補所花費的時間超出預期，且維護時間範圍少於 20 分鐘，則不會重新開機。

在 Azure 自動化中，會由 Runbook 安裝更新。 您無法檢視這些 Runbook，而其也不需要任何設定。 更新部署在建立後便會建立排程，以在指定的時間為所包含的機器啟動主要更新 Runbook。 主要 Runbook 會在每個代理程式上啟動子 Runbook，以安裝必要的更新。

到了更新部署中指定的日期和時間時，目標機器就會以平行方式執行部署。 在安裝之前，系統會先執行掃描，以確認仍然需要更新。 針對 WSUS 用戶端電腦，若 WSUS 中並未核准更新，則更新部署會失敗。

不支援讓一部機器在多個 Log Analytics 工作區 (亦稱為多重主目錄) 註冊更新管理。

## <a name="clients"></a>用戶端

### <a name="supported-client-types"></a>支援的用戶端類型

下表列出更新評估和修補支援的作業系統。 修補需要混合式 Runbook 背景工作角色，當您啟用虛擬機器或伺服器進行更新管理管理時，會自動安裝此背景工作角色。 如需混合式 Runbook 背景工作角色系統需求的詳細資訊，請參閱 [部署 Windows 混合式 runbook 背景工作角色](../automation-windows-hrw-install.md) 和 [部署 Linux 混合式 Runbook 背景工作角色](../automation-linux-hrw-install.md)。

> [!NOTE]
> 只有在自動化帳戶和 Log Analytics 工作區[對應表](../how-to/region-mappings.md#supported-mappings) \(部分機器翻譯\) 中列出的特定區域，才支援 Linux 機器的更新評量。

|作業系統  |注意  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2(Datacenter/Standard)<br><br>Windows Server 2012 ||
|Windows Server 2008 R2 (RTM 和 SP1 Standard)| 更新管理支援此作業系統的評量和修補。 Windows Server 2008 R2 支援 [混合式 Runbook 背景工作角色](../automation-windows-hrw-install.md) 。 |
|CentOS 6 和 7 (x64)       | Linux 代理程式需要存取更新存放庫。 分類型修補需要 `yum`，才能傳回 CentOS 在其 RTM 版本中沒有的安全性資料。 如需 CentOS 上分類型修補的詳細資訊，請參閱 [Linux 上的更新分類](view-update-assessments.md#linux)。          |
|Red Hat Enterprise 6 和 7 (x64)      | Linux 代理程式需要存取更新存放庫。        |
|SUSE Linux Enterprise Server 12 (x64)     | Linux 代理程式需要存取更新存放庫。        |
|Ubuntu 14.04 LTS、16.04 LTS 和 18.04 LTS (x64)       |Linux 代理程式需要存取更新存放庫。         |

> [!NOTE]
> 您可以透過更新管理來管理 Azure 虛擬機器擴展集。 更新管理適用於執行個體本身，而不是基礎映像。 您將必須以累加方式排程更新，如此一來，就不會一次更新所有 VM 執行個體。 您可以遵循[將非 Azure 電腦新增至變更追蹤和清查](../automation-tutorial-installed-software.md#add-a-non-azure-machine-to-change-tracking-and-inventory)中的步驟，來新增虛擬機器擴展集的節點。

### <a name="unsupported-client-types"></a>不支援的用戶端類型

下表列出不支援的作業系統：

|作業系統  |注意  |
|---------|---------|
|Windows 用戶端     | 不支援用戶端作業系統 (例如 Windows 7 和 Windows 10)。<br> 針對 Azure Windows 虛擬桌面 (WVD)，建議用來<br> 若要管理更新，請 [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) Windows 10 用戶端電腦修補程式管理。 |
|Windows Server 2016 Nano Server     | 不支援。       |
|Azure Kubernetes Service 節點 | 不支援。 使用[在 Azure Kubernetes Service (AKS) 中將安全性和核心更新套用至 Linux 節點](../../aks/node-updates-kured.md)中所述的修補程序。|

### <a name="client-requirements"></a>用戶端需求

下列資訊描述作業系統特有的用戶端需求。 如需其他指導方針，請參閱[網路規劃](#ports)。 若要瞭解 TLS 1.2 的用戶端需求，請參閱 [Azure 自動化的 tls 1.2 強制](../automation-managing-data.md#tls-12-enforcement-for-azure-automation)。

#### <a name="windows"></a>Windows

Windows 代理程式必須設定為可與 WSUS 伺服器通訊，或需要存取 Microsoft Update。 對於混合式電腦，建議先將您的電腦連接到 [Azure Arc 啟用的伺服器](../../azure-arc/servers/overview.md)，然後使用 Azure 原則將「 [部署 log analytics 代理程式」指派給 windows Azure Arc 電腦](../../governance/policy/samples/built-in-policies.md#monitoring) 內建原則，以安裝適用于 windows 的 log analytics 代理程式。 或者，如果您打算使用適用於 VM 的 Azure 監視器監視電腦，請改用 [ [啟用適用於 VM 的 Azure 監視器](../../governance/policy/samples/built-in-initiatives.md#monitoring) ] 方案。

您可以搭配 Microsoft Endpoint Configuration Manager 使用更新管理。 若要深入了解整合案例，請參閱[整合更新管理與 Windows Endpoint Configuration Manager](mecmintegration.md)。 針對 Configuration Manager 環境中網站所管理的 Windows 伺服器，需要[適用於 Windows 的 Log Analytics 代理程式](../../azure-monitor/platform/agent-windows.md)。 

根據預設，從 Azure Marketplace 部署的 Windows Vm 會設定為從 Windows Update 服務接收自動更新。 當您將 Windows VM 新增至工作區時，此行為並不會變更。 如果您未使用更新管理主動管理更新，即會套用預設行為 (以自動套用更新)。

> [!NOTE]
> 您可以修改群組原則，以便只有使用者 (而非系統) 能夠對該機器執行重新開機。 如果更新管理無權在未與使用者手動互動的情況下將機器重新開機，則受控機器可能會停滯。 如需詳細資訊，請參閱[設定自動更新的群組原則設定](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)。

#### <a name="linux"></a>Linux

針對 Linux，機器必須能夠存取更新存放庫 (私人或公用)。 必須使用 TLS 1.1 或 TLS 1.2，才能與更新管理互動。 更新管理不支援已設定為向多個 Log Analytics 工作區回報之適用於 Linux 的 Log Analytics 代理程式。 該機器也必須安裝 Python 2.x。

> [!NOTE]
> 只有在特定區域中才支援 Linux 機器的更新評量。 請參閱自動化帳戶和 Log Analytics 工作區[對應表](../how-to/region-mappings.md#supported-mappings) \(部分機器翻譯\)。

對於混合式電腦，建議先將您的電腦連接到 [Azure Arc 啟用的伺服器](../../azure-arc/servers/overview.md)，然後使用 Azure 原則將「 [部署 log analytics 代理程式」指派給 linux Azure Arc 機器](../../governance/policy/samples/built-in-policies.md#monitoring) 內建原則，以安裝適用于 linux 的 log analytics 代理程式。 或者，如果您打算使用適用於 VM 的 Azure 監視器監視電腦，請改用 [ [啟用適用於 VM 的 Azure 監視器](../../governance/policy/samples/built-in-initiatives.md#monitoring) ] 方案。

從隨選 Red Hat Enterprise Linux 建立的 Vm (RHEL) 可在 Azure Marketplace 中取得的映射，以存取部署在 Azure 中的 [Red Hat 更新基礎結構 (RHUI) ](../../virtual-machines/workloads/redhat/redhat-rhui.md) 。 針對任何其他 Linux 發行版本，則必須使用該發行版本所支援的方法，從發行版本的線上檔案存放庫進行更新。

## <a name="permissions"></a>權限

若要建立及管理更新部署，您必須具有特定權限。 若要了解這些權限，請參閱[角色型存取 - 更新管理](../automation-role-based-access-control.md#update-management-permissions)。

## <a name="update-management-components"></a>更新管理元件

更新管理會使用此節所述的資源。 當您啟用更新管理時，這些資源會自動新增至您的自動化帳戶。

### <a name="hybrid-runbook-worker-groups"></a>混合式 Runbook 背景工作群組

當您啟用更新管理之後，任何直接連線到您 Log Analytics 工作區的 Windows 機器都會自動設定為混合式 Runbook 背景工作角色，以支援要支援更新管理的 Runbook。

更新管理所管理的每部 Windows 機器都會列於混合式背景工作角色群組窗格中，以作為自動化帳戶的系統混合式背景工作角色群組。 這些群組會使用 `Hostname FQDN_GUID` 命名慣例。 您不能讓這些群組以您帳戶中的 Runbook 為目標。 如果您嘗試，則嘗試會失敗。 這些群組僅用於支援更新管理。 若要深入瞭解如何查看已設定為混合式 Runbook 背景工作角色的 Windows 機器清單，請參閱「 [查看混合式 runbook 背景工作角色](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers)」。

如果您針對更新管理和混合式 Runbook 背景工作角色群組成員資格使用相同的帳戶，則可將 Windows 機器新增到自動化帳戶中的混合式 Runbook 背景工作角色群組，以支援自動化 Runbook。 此功能已新增至混合式 Runbook 背景工作角色 7.2.12024.0 版。

### <a name="management-packs"></a>管理組件

如果您的 Operations Manager 管理群組已[連線到 Log Analytics 工作區](../../azure-monitor/platform/om-agents.md)，則下列管理組件會安裝在 Operations Manager 中。 這些管理組件也會針對更新管理，安裝在直接連線的 Windows 機器上。 您不需要設定或管理這些管理組件。

* Microsoft System Center Advisor 更新評估智慧套件 (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新部署 MP

> [!NOTE]
> 如果您的 Operations Manager 1807 或 2019 管理群組已連線到 Log Analytics 工作區，且在管理群組中設定代理程式來收集記錄資料，則您需要覆寫參數 `IsAutoRegistrationEnabled`，並在 **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** 規則中將其設為 True。

如需如何更新管理組件的詳細資訊，請參閱[將 Operations Manager 連線到 Azure 監視器記錄](../../azure-monitor/platform/om-agents.md)。

> [!NOTE]
> 若要讓更新管理使用 Log Analytics 代理程式完全管理機器，您必須更新為適用於 Windows 的 Log Analytics 代理程式或適用於 Linux 的 Log Analytics 代理程式。 若要深入了解如何更新代理程式，請參閱[如何升級 Operations Manager 代理程式](/system-center/scom/deploy-upgrade-agents)。 在使用 Operations Manager 的環境中，您必須執行 System Center Operations Manager 2012 R2 UR 14 或更新版本。

## <a name="data-collection"></a>資料收集

### <a name="supported-sources"></a>支援的來源

下表描述更新管理所支援的連線來源：

| 連線的來源 | 支援 | 描述 |
| --- | --- | --- |
| Windows 代理程式 |是 |更新管理會從 Windows 代理程式收集系統更新的相關資訊，然後開始安裝必要更新。 |
| Linux 代理程式 |是 |更新管理會從 Linux 代理程式收集系統更新的相關資訊，然後在支援的發行版本上開始安裝必要更新。 |
| Operations Manager 管理群組 |是 |「更新管理」會從所連線之管理群組中的代理程式收集系統更新的相關資訊。<br/><br/>Operations Manager 代理程式不需要直接連線到 Azure 監視器記錄。 資料會從管理群組轉送至 Log Analytics 工作區。 |

### <a name="collection-frequency"></a>收集頻率

更新管理會使用下列規則來掃描受控機器的資料。 其可能需要針對儀表板花費 30 分鐘到 6 小時的時間，才能顯示來自受控機器的更新資料。

* 每部 Windows 機器：更新管理會針對每部機器每天執行兩次掃描。

* 每部 Linux 機器：更新管理每小時都會執行一次掃描。

針對使用更新管理的機器，透過 Azure 監視器記錄所取得的平均資料使用量大約是每個月 25 MB。 這只是個近似值，其會根據您的環境而變更。 我們建議您監視環境，以持續追蹤確切的使用量。 如需分析 Azure 監視器記錄資料使用量的詳細資訊，請參閱 [管理使用量和成本](../../azure-monitor/platform/manage-cost-storage.md)。

## <a name="network-planning"></a><a name="ports"></a>網路規劃

以下為「更新管理」特別需求的位址。 與這些位址的通訊皆經由連接埠 443 進行。

|Azure 公用  |Azure Government  |
|---------|---------|
|`*.ods.opinsights.azure.com`    | `*.ods.opinsights.azure.us`        |
|`*.oms.opinsights.azure.com`     | `*.oms.opinsights.azure.us`        |
|`*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`|
|`*.azure-automation.net` | `*.azure-automation.us`|

當您建立網路群組安全性規則或設定 Azure 防火牆以允許流量流向自動化服務和 Log Analytics 工作區時，請使用 [服務](../../virtual-network/service-tags-overview.md#available-service-tags)標籤 **GuestAndHybridManagement** 和 **AzureMonitor**。 這可簡化網路安全性規則的持續管理。 若要安全且私下地從您的 Azure Vm 連接到自動化服務，請參閱 [使用 Azure Private Link](../how-to/private-link-security.md)。 若要取得目前的服務標籤和範圍資訊，以納入您的內部部署防火牆設定，請參閱 [可下載的 JSON](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)檔案。

對於 Windows 機器，您也必須允許流量傳送到 Windows Update 所需的任何端點。 您可以在[與 HTTP/Proxy 相關的問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中，找到所需端點的更新清單。 如果您有本機 [Windows Update 伺服器](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)，也必須允許流量傳送到您 [WSUS 金鑰](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)中指定的伺服器。

對於 Red Hat Linux 機器，請參閱 [RHUI 內容傳遞伺服器的 IP](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) 以取得必要的端點。 對於其他 Linux 發行版本，請參閱您的提供者文件。

如需混合式 Runbook 背景工作角色所需連接埠的詳細資訊，請參閱[混合式 Runbook 背景工作角色的更新管理位址](../automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker)。

如果您的 IT 安全性原則不允許網路上的電腦連線到網際網路，您可以設定 [Log Analytics 閘道](../../azure-monitor/platform/gateway.md) ，然後將機器設定為透過閘道進行連線，以 Azure 自動化和 Azure 監視器。

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
|其他更新     | 本質上不重要或非安全性更新的所有其他更新。        |

>[!NOTE]
>只有在支援的 Azure 公用雲端區域中使用時，才能使用適用于 Linux 機器的更新分類。 使用下列國家/地區雲端區域中的更新管理時：
>* Azure 美國政府
>* 中國的世紀
>
> 沒有 Linux 更新的分類，而且它們會在 [ **其他更新** ] 類別下回報。 更新管理使用支援的發行版本所發佈的資料，尤其是其發行的 [橢圓](https://oval.mitre.org/) (開放式弱點和評定語言) 檔。 因為網際網路存取受限於這些國家雲端，所以更新管理無法存取及使用這些檔案。

針對 Linux，更新管理可以區別分類 **安全性** 和 **其他** 雲端中的重大更新和安全性更新，同時顯示因雲端中的資料擴充而顯示的評定資料。 針對修補，「更新管理」仰賴機器上可用的分類資料。 與其他發行版本不同，CentOS 在 RTM 版本中沒有此資訊可供使用。 如果您將 CentOS 機器設定為傳回以下命令的安全性資料，更新管理就能根據分類進行修補。

```bash
sudo yum -q --security check-update
```

目前沒有支援的方法可以在 CentOS 上啟用原生分類資料可用性。 目前，有限的支援會提供給可能已自行啟用此功能的客戶。

若要將 Red Hat Enterprise 版本 6 上的更新分類，您必須安裝 yum-security 外掛程式。 在 Red Hat Enterprise Linux 7 上，此外掛程式已經是 yum 本身的一部分，因此不需要安裝任何項目。 如需詳細資訊，請參閱下列 Red Hat [知識文章](https://access.redhat.com/solutions/10021) \(英文\)。

當您將更新排程在 Linux 機器上執行時，此範例會設定為只安裝符合 **安全性** 分類的更新，安裝的更新可能與相同，或者是符合此分類的更新子集。 當您執行 Linux 機器的 OS 更新的評估時，系統會使用 Linux 發行版本廠商提供的 [開放弱點和評定語言](https://oval.mitre.org/) (OVAL) 更新管理進行分類。

您可以根據 OVAL 檔案以 **安全性** 或 **其他** 的形式針對 Linux 更新進行分類，其中包括解決安全性問題或弱點的更新。 但是，當更新排程執行時，它會使用適當的套件管理員（例如 YUM、APT 或 ZYPPER）在 Linux 機器上執行，以進行安裝。 Linux 發行版本的套件管理員可能會有不同的機制來分類更新，其中的結果可能會與從 OVAL 檔案取得的結果不同（更新管理）。 若要手動檢查電腦並瞭解哪些更新是與您的套件管理員相關的安全性，請參閱 [疑難排解 Linux 更新部署](../troubleshoot/update-management.md#updates-linux-installed-different)。

## <a name="integrate-update-management-with-configuration-manager"></a>整合更新管理與 Configuration Manager

已投資 Microsoft Endpoint Configuration Manager 來管理電腦、伺服器和行動裝置的客戶，也需仰賴 Configuration Manager 的強度和成熟度，以協助管理軟體更新。 若要了解如何整合更新管理與 Configuration Manager，請參閱[整合更新管理與 Windows Endpoint Configuration Manager](mecmintegration.md)。

## <a name="third-party-updates-on-windows"></a>Windows 上的協力廠商更新

更新管理依賴本機設定的更新存放庫來更新支援的 Windows 系統 (WSUS 或 Windows Update)。 [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) 之類的工具允許您使用 WSUS 來匯入和發佈自訂更新。 此案例允許更新管理，更新透過協力廠商軟體使用 Configuration Manager 作為其更新存放庫的機器。 若要了解如何設定 Updates Publisher，請參閱[安裝Updates Publisher](/configmgr/sum/tools/install-updates-publisher)。

## <a name="enable-update-management"></a>啟用更新管理

以下是您可以啟用更新管理並選取要管理之機器的方式：

- 使用 Azure [Resource Manager 範本](enable-from-template.md) 將更新管理部署至新的或現有的自動化帳戶，並 Azure 監視器訂用帳戶中的 Log Analytics 工作區。 其不會設定應管理的機器範圍，這會在使用範本之後以個別步驟執行。

- 從您的 [自動化帳戶](enable-from-automation-account.md) ，適用于一或多個 azure 與非 azure 機器，包括已啟用 Arc 的伺服器。

- 使用 **AutomationSolution** [runbook](enable-from-runbook.md) 方法。

- 針對 Azure 入口網站中 [**虛擬機器**] 頁面的 [選取 Azure VM](enable-from-vm.md) 。 此案例適用於 Linux 與 Windows VM。

- 針對 [多個 Azure vm](enable-from-portal.md) ，請從 Azure 入口網站中的 [ **虛擬機器** ] 頁面選取它們。

> [!NOTE]
> 更新管理需要將 Log Analytics 工作區連結至您的自動化帳戶。 如需支援區域的確切清單，請參閱 [Azure 工作區對應](../how-to/region-mappings.md)。 區域對應並不會影響從您的自動化帳戶，在不同區域中管理 VM 的能力。

## <a name="next-steps"></a>後續步驟

* 如需使用更新管理的詳細資訊，請參閱 [管理 vm 的更新](manage-updates-for-vm.md)。

* 檢閱 [Azure 自動化常見問題](../automation-faq.md)中有關更新管理的常見問題集。
