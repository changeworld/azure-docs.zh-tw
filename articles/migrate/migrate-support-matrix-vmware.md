---
title: Azure 遷移中的 VMware 評估支援
description: 通過 Azure 遷移伺服器評估瞭解對 VMware VM 評估的支援。
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 03d07adb6f19346901286bdae148f95e68290e4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336868"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 評估的支援矩陣 

本文總結了為遷移到 Azure 做準備評估 VMware VM 的先決條件和支援要求。 如果要將 VMware VM 遷移到 Azure，請查看[遷移支援矩陣](migrate-support-matrix-vmware-migration.md)。

您可以使用[Azure 遷移：伺服器評估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具評估物理伺服器。 創建 Azure 遷移專案，然後將該工具添加到專案。 添加該工具後，將部署 Azure[遷移設備](migrate-appliance.md)。 設備不斷發現本地電腦，並將電腦中繼資料和效能資料發送到 Azure。 發現電腦後，您將發現的電腦收集到組中，並為組運行評估。

## <a name="limitations"></a>限制

**支援** | **詳細資料**
--- | ---
**專案限制** | 可以在 Azure 訂閱中創建多個專案。<br/><br/> 您可以在單個[專案中](migrate-support-matrix.md#azure-migrate-projects)發現和評估多達 35，000 個 VMware VM。 專案可以包括 VMware VM、物理伺服器和超 VM，每個虛擬機器都達到評估限制。
**發現** | Azure 遷移設備可以在 vCenter 伺服器上發現多達 10，000 個 VMware VM。
**評量** | 單個組中最多可以添加 35，000 台電腦。<br/><br/> 您可以在單個評估中評估多達 35，000 個 VM。

[瞭解有關評估的更多](concepts-assessment-calculation.md)詳細資訊。


## <a name="application-discovery"></a>應用程式探索

除了發現電腦之外，Azure 遷移：伺服器評估還可以發現在電腦上運行的應用、角色和功能。 通過發現應用清單，您可以識別和規劃針對本地工作負荷量身定制的遷移路徑。 

**支援** | **詳細資料**
--- | ---
**支援的電腦** | 當前僅支援本地 VMware VM 的應用發現。
**發現** | 應用發現是無代理的。 它使用電腦來賓憑據，並使用 WMI 和 SSH 調用遠端存取電腦。
**虛擬機器支援** | 支援所有 Windows 和 Linux 版本的應用發現。
**vCenter 憑據** | 應用發現需要一個 vCenter Server 帳戶，該帳戶具有唯讀存取權限，並為虛擬機器啟用了>來賓操作的許可權。
**VM 憑據** | 應用發現當前支援對所有 Windows 伺服器使用一個憑據，支援對所有 Linux 伺服器使用一個憑據。<br/><br/> 為 Windows VM 創建來賓使用者帳戶，為所有 Linux VM 創建常規/正常使用者帳戶（非 sudo 訪問）。
**VMware 工具** | VMware 工具必須在要發現的 VM 上安裝和運行。 <br/> VMware 工具版本必須晚于 10.2.0。
**電源外殼** | VM 必須安裝 PowerShell 版本 2.0 或更高版本。
**埠訪問** | 在運行要發現的 VM 的 ESXi 主機上，Azure 遷移設備必須能夠連接到 TCP 埠 443。
**限制** | 對於應用發現，您可以發現每個 Azure 遷移設備上最多 10000 個 VM。



## <a name="vmware-requirements"></a>VMware 要求

**Vmware** | **詳細資料**
--- | ---
**VMware VM** | 支援所有 Windows 和 Linux 作業系統的評估。
**vCenter 伺服器** | 要發現和評估的電腦必須由 vCenter Server 版本 5.5、6.0、6.5 或 6.7 進行管理。
**許可權（評估）** | vCenter 伺服器唯讀帳戶。
**許可權（應用發現）** | vCenter 伺服器帳戶具有唯讀存取權限，並為虛擬機器啟用了>**來賓操作**的許可權。
**許可權（依賴項視覺化）** | 具有唯讀存取權限的中央伺服器帳戶，並為**虛擬機器** > **來賓操作**啟用了許可權。


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 設備需求

Azure 遷移使用[Azure 遷移設備](migrate-appliance.md)進行發現和評估。 VMware 的設備使用導入到 vCenter 伺服器的 OVA 範本部署。 

- 瞭解 VMware[的設備要求](migrate-appliance.md#appliance---vmware)。
- 瞭解設備需要訪問的[URL。](migrate-appliance.md#url-access)

## <a name="port-access"></a>埠訪問

**裝置** | **連接**
--- | ---
Appliance (設備) | TCP 埠 3389 上的入站連接，以允許遠端桌面連線到設備。<br/><br/> 埠 44368 上的入站連接，用於使用 URL 遠端存取裝置管理應用：```https://<appliance-ip-or-name>:44368``` <br/><br/>埠 443 （HTTPS） 上的出站連接，用於向 Azure 遷移發送發現和性能中繼資料。
vCenter 伺服器 | TCP 埠 443 上的入站連接，允許設備收集配置和性能中繼資料以進行評估。 <br/><br/> 預設情況下，設備連接到埠 443 上的 vCenter。 如果 vCenter 伺服器偵聽其他埠，則可以在設置發現時修改埠。
ESXi 主機（應用發現/無代理依賴關係分析） | 如果要執行[應用發現](how-to-discover-applications.md)或[無代理依賴項分析](concepts-dependency-visualization.md#agentless-analysis)，則設備連接到 TCP 埠 443 上的 ESXi 主機，以發現應用程式，並在 VM 上運行無代理依賴項視覺化。

## <a name="agentless-dependency-analysis-requirements"></a>無代理依賴項分析要求

[依賴項分析](concepts-dependency-visualization.md)可説明您確定要評估和遷移到 Azure 的本地電腦之間的依賴關係。 該表總結了設置無代理依賴項分析的要求。 

**要求** | **詳細資料**
--- | --- 
**部署前** | 應使用 Azure 遷移專案，將 Azure 遷移：伺服器評估工具添加到專案中。<br/><br/>  設置 Azure 遷移設備以發現本地 VMWare 電腦後部署依賴項視覺化。<br/><br/> [瞭解如何](create-manage-projects.md)首次創建專案。<br/> [瞭解如何](how-to-assess.md)將評估工具添加到現有專案。<br/> [瞭解如何](how-to-set-up-appliance-vmware.md)設置 Azure 遷移設備以評估 VMware VM。
**虛擬機器支援** | 當前僅支援 VMware VM。
**Windows VM** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows 伺服器 2008 R2 （64 位）。
**Windows 帳戶** |  對於依賴項分析，Azure 遷移設備需要本地或域管理員帳戶來訪問 Windows VM。
**Linux VM** | 紅帽企業 Linux 7、 6、 5<br/> Ubuntu Linux 14.04， 16.04<br/> Debian 7、8<br/> 甲骨文 Linux 6， 7<br/> CentOS 5， 6， 7.
**Linux 帳戶** | 對於依賴項分析，在 Linux 電腦上，Azure 遷移設備需要具有 Root 許可權的使用者帳戶。<br/><br/> 或者，使用者帳戶需要 /bin/netstat 和 /bin/ls 檔的這些許可權：CAP_DAC_READ_SEARCH和CAP_SYS_PTRACE。
**必需的代理** | 要分析的電腦上不需要代理。
**VMware Tools** | VMware 工具（晚于 10.2）必須在要分析的每個 VM 上安裝和運行。
**vCenter 伺服器憑據** | 依賴項視覺化需要具有唯讀存取權限的 vCenter Server 帳戶，並為虛擬機器啟用>來賓操作的許可權。 
**電源外殼** | VM 必須安裝 PowerShell 版本 2.0 或以上。
**埠訪問** | 在運行要分析的 VM 的 ESXi 主機上，Azure 遷移設備必須能夠連接到 TCP 埠 443。

## <a name="agent-based-dependency-analysis-requirements"></a>基於代理的依賴項分析要求

[依賴項分析](concepts-dependency-visualization.md)可説明您確定要評估和遷移到 Azure 的本地電腦之間的依賴關係。 該表總結了設置基於代理的依賴項分析的要求。 

**要求** | **詳細資料** 
--- | --- 
**部署前** | 應使用 Azure 遷移專案，將 Azure 遷移：伺服器評估工具添加到專案中。<br/><br/>  設置 Azure 遷移設備以發現本地電腦後部署依賴項視覺化<br/><br/> [瞭解如何](create-manage-projects.md)首次創建專案。<br/> [瞭解如何](how-to-assess.md)將評估工具添加到現有專案。<br/> 瞭解如何設置 Azure 遷移設備以評估[Hyper-V、VMware](how-to-set-up-appliance-hyper-v.md)或物理伺服器。 [VMware](how-to-set-up-appliance-vmware.md)
**Azure Government** | 依賴項視覺化在 Azure 政府中不可用。
**日誌分析** | Azure 遷移使用[Azure 監視器日誌](../log-analytics/log-analytics-overview.md)中的[服務映射](../operations-management-suite/operations-management-suite-service-map.md)解決方案進行依賴項視覺化。<br/><br/> 將新的或現有的日誌分析工作區與 Azure 遷移專案相關聯。 添加 Azure 遷移專案的工作區後無法對其進行修改。 <br/><br/> 工作區必須與 Azure 遷移專案處於同一訂閱中。<br/><br/> 工作區必須位於美國東部、東南亞或西歐區域。 其他區域中的工作區不能與專案關聯。<br/><br/> 工作區必須位於[支援服務映射](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的區域。<br/><br/> 在日誌分析中，與 Azure 遷移關聯的工作區將使用遷移專案鍵和專案名稱進行標記。
**必需的代理** | 在要分析的每台電腦上，安裝以下代理：<br/><br/> [微軟監控代理 （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [依賴項代理](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果本地電腦未連接到互聯網，則需要在它們上下載並安裝日誌分析閘道。<br/><br/> 瞭解有關安裝[依賴項代理](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)和[MMA](how-to-create-group-machine-dependencies.md#install-the-mma)的更多詳細資訊。
**日誌分析工作區** | 工作區必須與 Azure 遷移專案處於同一訂閱中。<br/><br/> Azure 遷移支援駐留在美國東部、東南亞和西歐區域的工作區。<br/><br/>  工作區必須位於[支援服務映射](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)的區域。<br/><br/> 添加 Azure 遷移專案的工作區後無法對其進行修改。
**成本** | 服務映射解決方案在前 180 天內不產生任何費用（從將日誌分析工作區與 Azure 遷移專案關聯的當天起）/<br/><br/> 經過 180 天後，會套用標準 Log Analytics 費用。<br/><br/> 在關聯的日誌分析工作區中使用服務映射以外的任何解決方案將會產生日誌分析[的標準費用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 刪除 Azure Migrate 專案時，工作區不會隨之刪除。 刪除專案後，服務映射使用不是免費的，每個節點將按日誌分析工作區的付費層收費/<br/><br/>如果在 Azure 遷移通用性（GA- 2018 年 2 月 28 日）之前創建的專案，則可能已產生額外的服務映射費用。 為了確保僅在 180 天后付款，我們建議您創建新專案，因為 GA 之前的現有工作區仍可收費。
**管理** | 將代理註冊到工作區時，可以使用 Azure 遷移專案提供的 ID 和金鑰。<br/><br/> 您可以使用 Azure Migrate 外部的 Log Analytics 工作區。<br/><br/> 如果刪除關聯的 Azure 遷移專案，則不會自動刪除工作區。 [手動刪除它](../azure-monitor/platform/manage-access.md)。<br/><br/> 除非刪除 Azure 遷移專案，否則不要刪除 Azure 遷移創建的工作區。 如果這樣做，相依性視覺效果功能不會如預期般運作。
**互聯網連接** | 如果電腦未連接到互聯網，則需要在它們上安裝日誌分析閘道。


## <a name="next-steps"></a>後續步驟

- [查看](best-practices-assessment.md)創建評估的最佳做法。
- [準備 VMware](tutorial-prepare-vmware.md)評估。
