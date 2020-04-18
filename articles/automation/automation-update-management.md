---
title: Azure 中的更新管理解決方案
description: 本文介紹如何使用 Azure 更新管理解決方案來管理 Windows 和 Linux 電腦的更新。
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: 9bdc8cf97513854cf6a92ffd078febca6302d35c
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617411"
---
# <a name="update-management-solution-in-azure"></a>Azure 中的更新管理解決方案

可以使用 Azure 自動化中的**更新管理解決方案**管理 Azure 中的 Windows 和 Linux 電腦的作業系統更新、本地環境和其他雲端環境中的作業系統更新。 您可以快速評估所有代理電腦上可用更新的狀態,並管理安裝伺服器所需更新的過程。

您可以使用以下方法為虛擬機器 (VM) 啟用更新管理:

- 從[Azure 自動化帳戶](automation-onboard-solutions-from-automation-account.md)中考慮一個或多個 Azure 電腦,並手動為非 Azure 電腦。

- 對於 Azure 門戶中的虛擬機器頁面中的單個 Azure VM。 此方案適用於[Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management)和[Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) VM。

- 通過從 Azure 門戶中的虛擬機頁面選擇多個[Azure VM。](manage-update-multi.md) 

> [!NOTE]
> 更新**管理解決方案**需要將日誌分析工作區連結到您的自動化帳戶。 有關受支援區域的最終清單,請參閱[Azure 工作區映射](./how-to/region-mappings.md)。 區域映射不會影響在獨立於自動化帳戶的單獨區域中管理 VM 的能力。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure[資源管理員範本](automation-update-management-deploy-template.md)可説明您將**更新管理解決方案**部署到訂閱中的新或現有自動化帳戶和日誌分析工作區。

## <a name="solution-overview"></a>解決方案概觀

由更新管理管理的電腦使用以下設定執行評估和更新部署:

* 適用於 Windows 或 Linux 的紀錄分析代理
* 適用於 Linux 的 PowerShell 預期狀態組態 (DSC)
* 自動化 Hybrid Runbook Worker
* 適用於 Windows 電腦的微軟更新或 Windows 伺服器更新服務 (WSUS)

下圖說明了解決方案如何評估和應用安全更新到工作區中的所有連接的 Windows 伺服器和 Linux 電腦:

![更新管理程序流程](./media/automation-update-management/update-mgmt-updateworkflow.png)

您可以使用更新管理在相同租用戶中的多個訂用帳戶中以原生方式上架機器。

包發佈後,補丁需要 2 到 3 小時才能顯示給 Linux 電腦進行評估。 對於 Windows 計算機,修補程式在發佈後需要 12 到 15 小時才能顯示進行評估。

計算機完成更新合規性掃描後,代理將資訊批量轉發到 Azure 監視器日誌。 默認情況下,在 Windows 計算機上,合規性掃描每 12 小時運行一次。

除了掃描計畫外,更新合規性掃描將在重新啟動日誌分析代理、更新安裝之前和更新安裝后的 15 分鐘內啟動。

默認情況下,對於 Linux 計算機,每小時執行一次合規性掃描。 如果重新啟動日誌分析代理,將在 15 分鐘內啟動合規性掃描。

該解決方案報告電腦的最新基於您配置為同步的源。 如果 Windows 計算機設定為向 WSUS 報告,具體取決於 WSUS 上次與 Microsoft 更新同步的次,結果可能與 Microsoft 更新顯示的結果不同。 對於配置為向本地回購而不是公共回購報告的 Linux 計算機,此行為是相同的。

> [!NOTE]
> 為了能正確地向服務回報，「更新管理」需要啟用特定 URL 和連接埠。 若要深入了解這些需求，請參閱[混合式背景工作角色的網路規劃](automation-hybrid-runbook-worker.md#network-planning)。

您可以透過建立計畫部署在需要更新的電腦上部署和安裝軟體更新。 分類為 *「可選」* 的更新不包括在 Windows 計算機的部署範圍內。 部署範圍中僅包含必要更新。

計畫部署定義哪些目標計算機接收適用的更新。 為此,它要麼顯式指定某些計算機,要麼選擇基於一組特定計算機的日誌搜索[的計算機組](../azure-monitor/platform/computer-groups.md)(或者基於基於指定條件動態選擇 Azure VM 的[Azure 查詢](automation-update-management-query-logs.md))。 這些組與[範圍配置](../azure-monitor/insights/solution-targeting.md)不同,範圍配置僅用於確定哪些計算機獲取啟用解決方案的管理包。

您還可以指定計劃來批准和設置可以安裝更新的時間段。 此期間稱為維護視窗。 如果需要一個維護視窗,並且選擇了適當的重新啟動選項,則保留 20 分鐘的維護視窗範圍用於重新啟動。 如果修補時間比預期長,並且維護視窗中的時間少於 20 分鐘,則不會發生重新啟動。

在 Azure 自動化中，會由 Runbook 安裝更新。 您無法查看這些 Runbook,並且它們不需要任何配置。 創建更新部署時,它會創建一個計劃,在指定時間為包含的計算機啟動主更新 Runbook。 主要 Runbook 會在每個代理程式上啟動子 Runbook，以安裝必要的更新。

在更新部署中指定的日期和時間,目標計算機並行執行部署。 在安裝之前，系統會先執行掃描，以確認仍然需要更新。 對於 WSUS 用戶端電腦,如果更新在 WSUS 中未獲得批准,則更新部署將失敗。

不支援在多個日誌分析工作區(也稱為多宿主)中註冊計算機以進行更新管理。

## <a name="clients"></a>用戶端

### <a name="supported-client-types"></a>支援的用戶端類型

下表列出了用於更新評估的支援操作系統。 修補需要混合 Runbook 輔助角色。 有關混合 Runbook 輔助角色要求的資訊,請參閱安裝 Windows[混合 Runbook 輔助角色](automation-windows-hrw-install.md)和[Linux 混合 Runbook 輔助項](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)的安裝指南。

|作業系統  |注意  |
|---------|---------|
|Windows 伺服器 2019 (資料中心/資料中心核心/標準)<br><br>Windows 伺服器 2016 (資料中心/資料中心核心/標準)<br><br>Windows 伺服器 2012 R2 (資料中心/標準)<br><br>Windows Server 2012 || 
|Windows 伺服器 2008 R2 (RTM 和 SP1 標準)| 更新管理僅支援此操作系統執行評估,不支援修補,因為 Windows Server 2008 R2 不支援[混合 Runbook 輔助角色](automation-windows-hrw-install.md)。 |
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理需要存取更新儲存庫。 基於分類的修補需要`yum`返回 CentOS 在其 RTM 版本中沒有的安全數據。 有關 CentOS 上基於分類的修補的詳細資訊,請參閱[在 Linux 上更新分類](automation-view-update-assessments.md#linux-2)。          |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理需要存取更新儲存庫。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理需要存取更新儲存庫。        |
|Ubuntu 14.04 LTS、16.04 LTS 和 18.04 (x86/x64)      |Linux 代理需要存取更新儲存庫。         |

> [!NOTE]
> Azure 虛擬機縮放集可以通過更新管理進行管理。 更新管理適用於實例本身,而不是基本映射。 您需要以增量方式安排更新,以便不會同時更新所有 VM 實例。
> 可以通過按照[板載非 Azure 計算機](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)下的步驟添加虛擬機縮放集的節點。

### <a name="unsupported-client-types"></a>不支援的用戶端類型

下表列不支援的作業系統:

|作業系統  |注意  |
|---------|---------|
|Windows 用戶端     | 不支援用戶端作業系統 (例如 Windows 7 和 Windows 10)。        |
|Windows Server 2016 Nano Server     | 不支援。       |
|Azure 庫伯奈斯服務節點 | 不支援。 使用[Azure Kubernetes 服務 (AKS) 中將安全性和內核更新應用於 Linux 節點中描述的](../aks/node-updates-kured.md)修補過程|

### <a name="client-requirements"></a>用戶端需求

以下資訊描述了特定於操作系統的用戶端要求。 有關其他指導,請參閱[網路規劃](#ports)。

#### <a name="windows"></a>Windows

必須將 Windows 代理設定為與 WSUS 伺服器通訊,否則它們需要存取 Microsoft 更新。

您可以將更新管理與配置管理器一起使用。 要瞭解有關整合式方案的詳細資訊,請參考[設定管理員與更新管理整合](oms-solution-updatemgmt-sccmintegration.md#configuration)。 日誌[分析視窗代理](../azure-monitor/platform/agent-windows.md)是必需的。 如果您要載入 Azure VM,則會自動安裝代理。

默認情況下,從 Azure 應用商店部署的 Windows VM 設置為從 Windows 更新服務接收自動更新。 當您添加此解決方案或將 Windows VM 添加到工作區時,此行為不會更改。 如果您未使用這個解決方案來主動管理更新，則會套用預設行為 (自動套用更新)。

> [!NOTE]
> 使用者可以修改組策略,以便電腦重新啟動只能由使用者執行,而不能由系統執行。 如果更新管理沒有權利在沒有使用者手動交互的情況下重新啟動計算機,則託管計算機可能會卡住。
>
> 有關詳細資訊,請參閱[為自動更新設定群組政策設定](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)。

#### <a name="linux"></a>Linux

對於 Linux,電腦需要存取更新儲存庫。 更新存放庫可以是私人或公用。 必須使用 TLS 1.1 或 TLS 1.2，才能與更新管理互動。 此解決方案不支援配置為向多個日誌分析工作區報告的 Linux 日誌分析代理。 電腦還必須安裝 Python 2.x。

有關如何為 Linux 安裝紀錄分析代理並下載最新版本的資訊,請參閱[Linux 的紀錄分析代理](../azure-monitor/platform/agent-linux.md)。 有關如何安裝 Windows 的紀錄分析代理的資訊,請參閱將[Windows 電腦連線到 Azure 監視器](../log-analytics/log-analytics-windows-agent.md)。

從 Azure 應用商店中可用的按需紅帽企業 Linux (RHEL) 映射創建的 VM 將註冊以訪問部署在 Azure 中的[紅帽更新基礎結構 (RHUI)。](../virtual-machines/workloads/redhat/redhat-rhui.md) 任何其他 Linux 發行版都必須使用分發支援的方法從分發的線上檔存儲庫中更新。

## <a name="permissions"></a>權限

若要建立及管理更新部署，您必須具有特定權限。 要瞭解這些權限,請參閱[基於角色的存取及更新管理](automation-role-based-access-control.md#update-management-permissions)。

## <a name="solution-components"></a>方案元件

解決方案包含下列資源。 啟用解決方案時,這些資源將自動添加到自動化帳戶中。 

### <a name="hybrid-worker-groups"></a>混合式背景工作群組

啟用此解決方案後,直接連接到日誌分析工作區的任何 Windows 電腦將自動配置為混合 Runbook 輔助角色,以支援此解決方案中包含的 Runbook。

由解決方案管理的每個 Windows 計算機都作為自動化帳戶的系統混合輔助角色組列在「混合輔助角色群組」 面板中。 解決方案使用`Hostname FQDN_GUID`命名約定。 您不能讓這些群組以您帳戶中的 Runbook 為目標。 如果嘗試,嘗試失敗。 這些組旨在僅支援此管理解決方案。

如果對解決方案和混合 Runbook 輔助角色組成員身份使用相同的帳戶,則可以將 Windows 電腦添加到自動化帳戶中的「混合 Runbook 輔助角色」組,以支援自動化 Runbook。 此功能已新增至混合式 Runbook 背景工作角色 7.2.12024.0 版。

### <a name="management-packs"></a>管理組件

如果系統中心操作管理員管理組[連接到日誌分析工作區](../azure-monitor/platform/om-agents.md),則操作管理器中將安裝以下管理包。 添加解決方案後,這些管理包也會安裝在直接連接的 Windows 電腦上。 您不需要設定或管理這些管理組件。

* Microsoft System Center Advisor 更新評估智慧套件 (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新部署 MP

> [!NOTE]
> 如果操作管理器 1807 或 2019 管理組連接到日誌分析工作區,管理組中配置了代理以收集日誌數據`IsAutoRegistrationEnabled`,則需要在**Microsoft.IntelligencePack.Azure自動化.HybridAgent.Init**規則中重寫該參數並將其設置為 True。

有關如何更新解決方案管理包的詳細資訊,請參閱[將作業管理員連接到 Azure 監視器紀錄](../azure-monitor/platform/om-agents.md)。

> [!NOTE]
> 對於具有操作管理器代理的電腦,必須由更新管理完全管理,必須將代理更新為 Windows 的日誌分析代理或 Linux 的日誌分析代理。 若要深入了解如何更新代理程式，請參閱[如何升級 Operations Manager 代理程式](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)。 在使用操作管理員的環境中,必須運行系統中心操作管理器 2012 R2 UR 14 或更高版本。

## <a name="data-collection"></a>資料收集

### <a name="supported-agents"></a>支援的代理程式

下表描述本解決方案支援的連線來源：

| 連線的來源 | 支援 | 描述 |
| --- | --- | --- |
| Windows 代理程式 |是 |解決方案會從 Windows 代理程式收集系統更新的相關資訊，然後起始必要更新的安裝。 |
| Linux 代理程式 |是 |解決方案會從 Linux 代理程式收集系統更新的相關資訊，然後在支援的發行版本上起始必要更新的安裝。 |
| Operations Manager 管理群組 |是 |方案會從所連線之管理群組中的代理程式收集系統更新的相關資訊。<br/><br/>不需要從操作管理器代理到 Azure 監視器日誌的直接連接。 資料會從管理群組轉送至 Log Analytics 工作區。 |

### <a name="collection-frequency"></a>收集頻率

掃描每天為每個託管 Windows 計算機執行兩次。 系統會每隔 15 分鐘呼叫一次 Windows API 來查詢上次更新時間，以判斷狀態是否已變更。 如果狀態已變更，則會起始合規性掃描。

每台託管 Linux 計算機每小時執行一次掃描。

儀表板可能需要 30 分鐘到 6 小時才能顯示來自託管計算機的更新數據。

使用更新管理的電腦的 Azure 監視器日誌的平均數據使用量約為每月 25 MB。 此值僅是近似值,可能會更改,具體取決於您的環境。 我們建議您監視環境以跟蹤您的確切使用方式。 有關詳細資訊,請參閱[管理使用方式和成本](../azure-monitor/platform/manage-cost-storage.md)。

## <a name="network-planning"></a><a name="ports"></a>網路規劃

以下為「更新管理」特別需求的位址。 與這些位址的通訊皆經由連接埠 443 進行。

|Azure 公用  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

對於 Windows 計算機,還必須允許流量到 Windows 更新所需的任何終結點。 您可以在[與 HTTP/代理相關的問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中找到所需終結點的更新清單。 如果您有本地 Windows[更新伺服器](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment),還必須允許流量到[WSUS 密鑰](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)中指定的伺服器。

有關紅帽 Linux 計算機,請參閱有關所需終結點[的 RHUI 內容交付伺服器的 IP。](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) 有關其他 Linux 發行版,請參閱提供程式文檔。

有關混合 Runbook 工作線程所需的連接埠的詳細資訊,請參閱[混合輔助角色連接埠](automation-hybrid-runbook-worker.md#hybrid-worker-role)。

我們建議您在定義異常時使用列出的位址。 IP 位址,可以下載[Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 此檔每周更新一次,它反映當前部署的範圍和對 IP 範圍的任何即將更改。

按照[連接沒有 Internet 的電腦中的](../azure-monitor/platform/gateway.md)說明進行設定沒有 Internet 存取的電腦。

## <a name="update-classifications"></a>更新分類

下表列出「更新管理」中的更新分類清單，以及每個分類的定義。

### <a name="windows"></a>Windows

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

### <a name="linux"></a><a name="linux-2"></a>Linux

|分類  |描述  |
|---------|---------|
|重大更新和安全性更新     | 特定問題或特定產品的安全性相關問題的更新，         |
|其他更新     | 所有其他不在性質上不重要的更新或不是安全更新的更新。        |

對於 Linux,更新管理可以區分雲中的關鍵更新和安全更新,同時顯示由於雲中的數據豐富而導致的評估數據。 針對修補，「更新管理」仰賴機器上可用的分類資料。 與其他發行版不同,CentOS 在 RTM 版本中沒有此資訊。 如果配置了 CentOS 計算機以返回以下命令的安全數據,則更新管理可以基於分類進行修補。

```bash
sudo yum -q --security check-update
```

目前沒有支援在 CentOS 上啟用本機分類數據可用性的方法。 此時,僅向可能自行啟用此功能的客戶提供盡力而為的支援。 

要對紅帽企業版 6 上的更新進行分類,您需要安裝 yum-安全外掛程式。 在紅帽企業Linux 7上,外掛程式已經是yum本身的一部分,沒有必要安裝任何東西。 有關詳細資訊,請參閱以下紅帽[知識文章](https://access.redhat.com/solutions/10021)。

## <a name="integrate-with-configuration-manager"></a>與 Configuration Manager 整合

投資 Microsoft 端點配置管理員來管理 PC、伺服器和行動裝置的客戶還依靠配置管理器的優勢和成熟度來幫助他們管理軟體更新。 Configuration Manager 是其軟體更新管理 (SUM) 週期的一部分。

要瞭解如何將管理解決方案與設定管理員整合,請參考[設定管理員與更新管理整合](oms-solution-updatemgmt-sccmintegration.md)。

### <a name="third-party-updates-on-windows"></a>Windows 上的第三方更新

更新管理依賴於本地配置的更新儲存庫來更新支援的 Windows 系統。 這是 WSUS 或 Windows 更新。 [系統中心更新發佈者](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher)(更新發行者)等工具允許您使用 WSUS 導入和發佈自定義更新。 此方案允許更新管理使用第三方軟體更新使用配置管理器作為更新儲存庫的計算機。 若要了解如何設定 Updates Publisher，請參閱[安裝Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher)。

## <a name="enable-update-management"></a><a name="onboard"></a>開啟更新管理

要開始更新系統,您需要啟用更新管理解決方案。 以下是用來加入解決方案的建議與支援的方法:

- [從虛擬機器](automation-onboard-solutions-from-vm.md)
- [從瀏覽多部電腦](automation-onboard-solutions-from-browse.md)
- [從自動化帳戶](automation-onboard-solutions-from-automation-account.md)
- [使用 Azure 自動化 Runbook](automation-onboard-solutions.md)
- [使用 Azure 資源管理員樣本](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>後續步驟

查看 Azure 自動化[常見問題解答](automation-faq.md),查看有關此解決方案的常見問題。
